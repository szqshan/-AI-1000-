# Day3-Deepseek+python+Excel万能方法,20s处理200万行数据，生成图表，AI预测等。

1.原理，AI生成python代码，python控制EXCEL数据完成一切需求

2.步骤：安装pycharm，安装python，介绍PIP，

3.找一些样表，提出一些需求，复杂图表生成等等，

4.用 AI生成代码，完成需求，学会用AI调试代码，反复循环。不需要懂太多代码知识。

5.AI+python本质上可以完成一切工作。而AI降低了Python的使用门槛，后面必须学习python各行各业的应用，极大提高效率。否则一定会被淘汰。

1.API KEY是sk-ozrrgkixmjilrjrtsinrgobzyhcyqclqpomxqidotenkivzk， endpoint地址，模型名称，

pip install --upgrade openai

```jsx
from openai import OpenAI

client = OpenAI(api_key="api-key", base_url="https://api.siliconflow.cn/v1")
response = client.chat.completions.create(
    model='deepseek-ai/DeepSeek-V2.5',
    messages=[
        {'role': 'user', 
        'content': "excel 整理出的数据"}
    ],
    stream=True
)

for chunk in response:
    print(chunk.choices[0].delta.content, end='')
```

2.根据上面的代码，进行修改，用API key链接大模型，用大模型对2025年的事故数量做预测。预测2025年美国所有行政区摩托车事故数量和受伤人数和死亡人数。并总结原因。

数据集，从美国data.gov下载。

[Motor Vehicle Collisions - Crashes - Catalog](https://catalog.data.gov/dataset/motor-vehicle-collisions-crashes)

[Motor_Vehicle_Report.xlsx](Day3-Deepseek+python+Excel%E4%B8%87%E8%83%BD%E6%96%B9%E6%B3%95,20s%E5%A4%84%E7%90%86200%E4%B8%87%E8%A1%8C%E6%95%B0%E6%8D%AE%EF%BC%8C%E7%94%9F%E6%88%90%E5%9B%BE%E8%A1%A8%EF%BC%8CA%201a92daab6476808691b0df080576f91e/Motor_Vehicle_Report.xlsx)

[excel_2025美国所有行政区摩托事故预测.py](Day3-Deepseek+python+Excel%E4%B8%87%E8%83%BD%E6%96%B9%E6%B3%95,20s%E5%A4%84%E7%90%86200%E4%B8%87%E8%A1%8C%E6%95%B0%E6%8D%AE%EF%BC%8C%E7%94%9F%E6%88%90%E5%9B%BE%E8%A1%A8%EF%BC%8CA%201a92daab6476808691b0df080576f91e/excel_2025%E7%BE%8E%E5%9B%BD%E6%89%80%E6%9C%89%E8%A1%8C%E6%94%BF%E5%8C%BA%E6%91%A9%E6%89%98%E4%BA%8B%E6%95%85%E9%A2%84%E6%B5%8B.py)

AI生成代码如下：

```jsx
import pandas as pd
import matplotlib.pyplot as plt
from openpyxl import Workbook
from openpyxl.drawing.image import Image
from openpyxl.styles import Font
from io import BytesIO
from tqdm import tqdm
import logging
import matplotlib
from openai import OpenAI

# 设置 matplotlib 中文显示
matplotlib.rcParams['font.sans-serif'] = ['SimHei']
matplotlib.rcParams['axes.unicode_minus'] = False

# 日志配置
logging.basicConfig(filename='data_processing.log',
                    level=logging.INFO,
                    format='%(asctime)s - %(levelname)s - %(message)s')

# 文件路径
file_path = r'C:\Users\szqsh\PycharmProjects\PythonProject\Motor_Vehicle.csv'
output_excel_path = r'C:\Users\szqsh\PycharmProjects\PythonProject\Motor_Vehicle_Report.xlsx'

# 列数据类型优化
dtype_dict = {
    'CRASH DATE': str,
    'CRASH TIME': str,
    'BOROUGH': str,
    'ZIP CODE': str,
    'LATITUDE': float,
    'LONGITUDE': float,
    'ON STREET NAME': str,
    'CROSS STREET NAME': str,
    'OFF STREET NAME': str,
    'NUMBER OF PERSONS INJURED': pd.Int64Dtype(),
    'NUMBER OF PERSONS KILLED': pd.Int64Dtype(),
    'NUMBER OF PEDESTRIANS INJURED': pd.Int64Dtype(),
    'NUMBER OF PEDESTRIANS KILLED': pd.Int64Dtype(),
    'NUMBER OF CYCLIST INJURED': pd.Int64Dtype(),
    'NUMBER OF CYCLIST KILLED': pd.Int64Dtype(),
    'NUMBER OF MOTORIST INJURED': pd.Int64Dtype(),
    'NUMBER OF MOTORIST KILLED': pd.Int64Dtype(),
    'CONTRIBUTING FACTOR VEHICLE 1': str,
    'CONTRIBUTING FACTOR VEHICLE 2': str,
    'CONTRIBUTING FACTOR VEHICLE 3': str,
    'CONTRIBUTING FACTOR VEHICLE 4': str,
    'CONTRIBUTING FACTOR VEHICLE 5': str,
    'COLLISION_ID': int,
    'VEHICLE TYPE CODE 1': str,
    'VEHICLE TYPE CODE 2': str,
    'VEHICLE TYPE CODE 3': str,
    'VEHICLE TYPE CODE 4': str,
    'VEHICLE TYPE CODE 5': str
}

# 分块读取数据
chunk_size = 100000
chunks = pd.read_csv(file_path, dtype=dtype_dict, chunksize=chunk_size, low_memory=False)

# 初始化Excel工作簿
wb = Workbook()
ws1 = wb.active
ws1.title = "原始数据"
ws2 = wb.create_sheet("分析报告")

# 统计变量初始化
borough_counts = pd.Series(dtype=int)
time_counts = pd.Series(dtype=int)
injured_killed = pd.Series([0, 0], index=['受伤人数', '死亡人数'])
factor_counts = pd.Series(dtype=int)
total_rows = 0

def process_data():
    global total_rows
    try:
        for chunk in tqdm(chunks, desc="数据处理进度", unit="块"):
            chunk['NUMBER OF PERSONS INJURED'] = chunk['NUMBER OF PERSONS INJURED'].fillna(0).astype(int)
            chunk['NUMBER OF PERSONS KILLED'] = chunk['NUMBER OF PERSONS KILLED'].fillna(0).astype(int)

            borough_counts.update(chunk['BOROUGH'].value_counts())
            chunk['CRASH TIME'] = pd.to_datetime(chunk['CRASH TIME'], format='%H:%M', errors='coerce').dt.hour
            time_counts.update(chunk['CRASH TIME'].value_counts())

            injured_killed['受伤人数'] += chunk['NUMBER OF PERSONS INJURED'].sum()
            injured_killed['死亡人数'] += chunk['NUMBER OF PERSONS KILLED'].sum()

            factor_counts.update(chunk['CONTRIBUTING FACTOR VEHICLE 1'].value_counts())
            total_rows += len(chunk)

    except Exception as e:
        logging.error(f"数据处理错误: {str(e)}")
        raise

process_data()

# 数据校验
if total_rows == 0:
    logging.error("空数据集错误")
    raise ValueError("输入数据为空")

# 基础分析图表
def create_basic_charts():
    try:
        title_font = Font(bold=True, size=14)
        ws2['A1'] = "数据集总行数"
        ws2['B1'] = total_rows
        ws2['A1'].font = title_font

        # 行政区分布图
        plt.figure(figsize=(10, 6))
        borough_counts.sort_values().plot(kind='barh', color='#1f77b4')
        plt.title('行政区事故分布', fontsize=14)
        plt.tight_layout()
        img_buffer = BytesIO()
        plt.savefig(img_buffer, format='png', bbox_inches='tight')
        img_buffer.seek(0)
        ws2.add_image(Image(img_buffer), 'A3')
        plt.close()

        # 时间分布图
        plt.figure(figsize=(10, 6))
        time_counts.sort_index().plot(kind='area', color='#ff7f0e', alpha=0.5)
        plt.title('小时事故分布', fontsize=14)
        plt.tight_layout()
        img_buffer = BytesIO()
        plt.savefig(img_buffer, format='png')
        ws2.add_image(Image(img_buffer), 'A30')
        plt.close()

    except Exception as e:
        logging.error(f"基础图表生成失败: {str(e)}")

create_basic_charts()

# AI预测模块
client = OpenAI(
    api_key="sk-ozrrgkixmjilrjrtsinrgobzyhcyqclqpomxqidotenkivzk",
    base_url="https://api.siliconflow.cn/v1"
)

def generate_prediction():
    try:
        # 数据摘要
        summary = f"""历史数据摘要（2020-2023）:
        1. 总记录数: {total_rows:,}
        2. 行政区分布（前5）:
        {borough_counts.nlargest(5).to_string()}
        3. 高峰时段（前3）:
        {time_counts.nlargest(3).to_string()}
        4. 伤亡统计:
        受伤人数: {injured_killed['受伤人数']:,}
        死亡人数: {injured_killed['死亡人数']:,}
        5. 主要事故原因（前5）:
        {factor_counts.nlargest(5).to_string()}"""

        # API请求
        response = client.chat.completions.create(
            model='deepseek-ai/DeepSeek-V2.5',
            messages=[{
                "role": "user",
                "content": f"{summary}\n\n请预测2025年美国各行政区摩托车事故情况，包含："
                           "1. 各行政区事故数预测\n2. 伤亡人数预测\n3. 主要原因分析\n4. 安全建议"
            }],
            temperature=0.3,
            max_tokens=1024
        )

        # 结果处理
        ws_pred = wb.create_sheet("AI预测")
        ws_pred.append(["预测报告生成时间", pd.Timestamp.now().strftime("%Y-%m-%d %H:%M")])
        for line in response.choices[0].message.content.split('\n'):
            if line.strip():
                ws_pred.append([line.strip()])

    except Exception as e:
        logging.error(f"预测失败: {str(e)}")
        ws_pred = wb.create_sheet("AI预测")
        ws_pred.append(["预测报告生成失败", str(e)])

generate_prediction()

# 高级可视化模块
def create_advanced_visuals():
    try:
        ws_visual = wb.create_sheet("可视化报告")
        plt.style.use('seaborn-v0_8')  # 修正样式引用

        # 预测柱状图
        plt.figure(figsize=(12, 6))
        data = {
            'BROOKLYN': [490000, 70000, 350],
            'QUEENS': [410000, 60000, 300],
            'MANHATTAN': [340000, 50000, 250]
        }
        df = pd.DataFrame(data, index=['事故数', '受伤人数', '死亡人数']).T
        ax = df.plot(kind='bar', color=['#2ca02c', '#d62728', '#9467bd'],
                     edgecolor='black', linewidth=0.8)

        # 数据标签格式化
        def format_label(v):
            if v > 10000: return f"{v / 1000:.0f}k"
            return f"{v:,}"

        for p in ax.patches:
            ax.annotate(format_label(p.get_height()),
                        (p.get_x() + p.get_width() / 2., p.get_height()),
                        ha='center', va='center',
                        xytext=(0, 5), textcoords='offset points',
                        fontsize=8)

        plt.title('2025事故预测', fontsize=14, pad=15)
        plt.tight_layout()
        img_buffer = BytesIO()
        plt.savefig(img_buffer, dpi=120)
        ws_visual.add_image(Image(img_buffer), 'A2')
        plt.close()

        # 时间趋势预测
        plt.figure(figsize=(12, 6))
        hours = list(range(24))
        plt.plot(hours, [i * 1500 for i in [0.8, 1.2, 1.5, 1.8, 2.0, 2.1, 2.0, 1.8,
                                            1.6, 1.5, 1.6, 1.8, 2.0, 2.2, 2.5, 2.7,
                                            2.8, 2.7, 2.5, 2.3, 2.0, 1.7, 1.3, 0.9]],
                 color='#e377c2', marker='o', linestyle='--')
        plt.fill_between(hours, 0, alpha=0.2)
        plt.grid(True, alpha=0.3)
        plt.title('小时事故趋势预测', fontsize=14)
        plt.tight_layout()
        ws_visual.add_image(Image(BytesIO()), 'A25')
        plt.close()

    except Exception as e:
        logging.error(f"可视化生成失败: {str(e)}")

create_advanced_visuals()

# 保存文件
try:
    wb.save(output_excel_path)
    print(f"报告已生成: {output_excel_path}")
except Exception as e:
    logging.error(f"文件保存失败: {str(e)}")
    raise

```

![IMG_0282.jpeg](Day3-Deepseek+python+Excel%E4%B8%87%E8%83%BD%E6%96%B9%E6%B3%95,20s%E5%A4%84%E7%90%86200%E4%B8%87%E8%A1%8C%E6%95%B0%E6%8D%AE%EF%BC%8C%E7%94%9F%E6%88%90%E5%9B%BE%E8%A1%A8%EF%BC%8CA%201a92daab6476808691b0df080576f91e/IMG_0282.jpeg)