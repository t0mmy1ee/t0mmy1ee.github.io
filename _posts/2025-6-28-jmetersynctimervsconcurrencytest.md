---
title: JMeter同步定时器与并发关系测试
description: 分析了并发测试时JMeter同步定时器的设置对测试的影响
author: yangchi
date: 2025-6-28 13:43:00 +0800
categories: [检测工具]
tags: [JMeter, 性能测试, 并发测试, 工具研究]
#pin: true
#math: true
#mermaid: true
image:
  path: /assets/img/2025-12-22-16-13-29.png
---

# 1.JMeter测试并发py验证
JMeter运行过程中能看到图形结果如图  
无法看出是否在某时间节点的并发情况，需要写个python脚本对统计结果结合分析  
![](2025-12-22-16-16-49.png)  
x轴表示时间轴，y轴表示响应时间  
JMeter 输出的结果文件jtl中包含时间戳字段（默认格式是毫秒级 Unix 时间戳）。  
使用Python 统计每个请求的时间间隔。  
如果发现多个请求集中在同一个时间点（例如相差几毫秒），则说明它们是并发执行的。  
 
```python
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib.dates import DateFormatter

# ================== 配置部分 ==================
jtl_file = 'test_4.jtl'      # 替换为你自己的 JTL 文件路径
output_image = 'concurrency_analysis_4b.png'  # 输出图表文件名
timeX = '1ms'                # 替换为你的 X 值，单位毫秒

# ================== 数据处理 ==================
# 读取 JMeter 结果文件
df = pd.read_csv(jtl_file)

# 将时间戳转换为 datetime 类型（毫秒级）
df['time'] = pd.to_datetime(df['timeStamp'], unit='ms')

# 设置时间为索引
df.set_index('time', inplace=True)

# 按X毫秒聚合请求数量
requests_per_Xms = df.resample(timeX).size()
print(requests_per_Xms)
# 可选：提取每个请求的“线程名”用于识别并发批次
# 如果你需要分析哪些请求来自同一批并发，可使用线程名或自定义变量
# 示例：假设你用 JSR223 添加了 batch_id 到响应数据中，也可以类似处理

# ================== 图形化展示 ==================
plt.figure(figsize=(16, 6))

# 绘制柱状图
ax = requests_per_Xms.plot(kind='bar', color='skyblue', edgecolor='black')

# 设置标题和标签
plt.title(f'Concurrency Pattern: Requests per {timeX}', fontsize=14)
plt.xlabel(f'Time (with {timeX} resolution)', fontsize=12)
plt.ylabel('Number of Requests', fontsize=12)

# 设置 x 轴格式（显示到毫秒）
def format_x_labels(x, pos=None):
    if x >= len(requests_per_Xms):
        return ''
    dt = requests_per_Xms.index[x]
    return dt.strftime('%H:%M:%S.%f')[:-3]  # 显示到毫秒

ax.xaxis.set_major_locator(plt.MaxNLocator(nbins=20))
ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: format_x_labels(int(x))))

# 旋转 x 轴标签
plt.xticks(rotation=45)
plt.tight_layout()

# 保存图像
plt.savefig(output_image, dpi=150)

# 显示图像
plt.show()

print(f"✅ 已生成图表：{output_image}")
```
{: file='验证并发的时间节点.py'}  

## 总结：

- 通过测试执行次数和执行持续时间两种情况都能证明，无同步定时器几乎不能达到并发条件（见2.1和2.2、见3.1和3.2）
- 通过测试线程数和同步定时器的设定数（可以理解为集合点数），得出如下结论（见2.3和2.4、见3.3和3.4）  
a.线程数小于集合点数，并发按照线程数为准；  
b.线程数大于集合点数，并发按照集合点数为准。


# 2.线程按**次数**执行，执行10次
1. 线程数等于定时器数，有同步定时器  
   ![](2025-12-22-16-38-55.png)  
   图，线程10同步定时器10_测试执行10次     
2. 线程数等于定时器数，**无同步定时器**  
   ![](2025-12-22-16-41-20.png)  
   图5、 jmeter设置10线程执行10次（无同步定时器为），时间间隔10ms  
   无同步定时器，设置10线程测试10次并不能达到10并发，从图5的X轴时间和Y轴的请求数能看出，  
   无同步定时器，并发最高只能到4 
3. 线程数小于定时器数  
   ![](2025-12-22-16-42-57.png)  
   如图所示，线程5同步定时器10_测试执行10次，jmeter不执行，跑了8分钟都run起来
4. 线程数大于定时器数  
   最大并发只能到5  
   ![](2025-12-22-16-45-19.png)  
   图，线程10同步定时器5_测试执行10次  
   **横轴**是精确到毫秒的时间点（如 04:55:47.400）  
   **纵轴**是该 100ms 时间窗口内的请求数  
   每个柱子代表一次并发行为（如 10 个请求）

# 3.线程按持续时间执行，持续10s
1. 使用同步定时器_测试持续10s
   ![](2025-12-22-16-51-36.png)  
   图jmeter运行中的图  
   ![](2025-12-22-16-51-57.png)      
2. 不使用同步定时器_测试持续10s
   ![](2025-12-22-16-52-21.png)  
   图 jmeter运行中的图  
   ![](2025-12-22-16-52-44.png)     
3. 同步定时器10线程5，持续10s
   ![](2025-12-22-16-48-44.png)  
   ![](2025-12-22-16-48-57.png)  
   时间间隔统计10ms  
   ![](2025-12-22-16-49-27.png)  
   时间间隔统计1ms  
   ![](2025-12-22-16-53-05.png)  
   同步定时器数大于线程数，持续10s，只在2025-06-27 06:51:31.476执行了一次5线程并发     
4. 同步定时器5线程10，持续10s
   ![](2025-12-22-16-54-13.png)  
   时间间隔统计10ms  
   ![](2025-12-22-16-54-33.png)  
   时间间隔统计1ms  
   同步定时器数小于线程数，持续10s，并发数以定时器数为准
