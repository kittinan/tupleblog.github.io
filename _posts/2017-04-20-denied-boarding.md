---
layout: post
title: Denied Boardings Data and Visualization
author: Titipata
description: สอนเขียน visualization จากโพสต์ของพี่ต้า วิโรจน์
image:
  feature:
tags: [Data, Data Science, Python]
comments: true
date: 2017-04-20 22:45:00
read_time: 10
---

# โฆษณาเล็กน้อยก่อนเริ่มโพสต์

พวกเรา tupleblog team กำลังจะจัด Python/R Data Science Meetup ที่ Hangar, Incubator space ของ DTAC กันในต้นเดือนสิงหาคมนี้!
เรามีพี่ต้า อดีต data scientist ที่ Facebook และเจ้าของโพสต์ "ส่องข้อมูลการปฏิเสธการขึ้นเครื่อง (Denied Boardings) ในอเมริกา"
ที่เราจะมาลองทำตามกันในโพสต์นี้ มาเป็น keynote speaker กันในครั้งแรก

นอกจากนั้นก็ยังมี speaker เจ๋งๆอีกหลายคน ไว้คอยติดตามกันต่อไป
ส่วนรายอะเอียดของงานกับการรับสมัครเราจะมาบอกกันอีกทีในอีกเดือนข้างหน้า คอยติดตามกันเร็วๆนี้นะ


ไม่ได้มีแค่ speaker เท่านั้น เรายังมีหนังสือจาก O'Reilly พร้อมลายเซ็นต์ได้แก่ Python Data Science Handbook ของ
Jake VanderPlas และ Data Science from Scratch: First Principles with Python ของ Joel Grus
ที่จะสุ่มแจกให้คนที่มา Meetup ครั้งแรกอีกด้วย!

เท่านี้แหละ พูดให้ตื่นเต้นกันไว้ก่อน ไปเริ่มเขียนโค้ดกันก่อนแล้วกัน

## อารัมบท

สำหรับโพสต์นี้เราจะมาว่ากันด้วยการพล็อตเป็นหลัก เราจะพล็อตแบบ interactive โดยใช้ไลบรารี่จาก Python
ที่ชื่อว่า Bokeh กัน

ตัวอย่างของ data ที่จะใช้วันนี้เป็น data ของสายการบิน (ที่ United เป็นข่าวใหญ่ลากลุงออกมาจากเครื่องบิน)
เราจะมาดูกันว่าอัตราการโดนไล่จากออกนั้นสูงแค่ไหนสำหรับแต่ละสายการบิน เราจะมาลองพล็อตเล่นๆโดยใช้ Bokeh
ตามโพสต์ต้นฉบับของพี่ต้ากัน อ่านได้เลย[ที่นี่](https://medium.com/skooldio/%E0%B8%AA%E0%B9%88%E0%B8%AD%E0%B8%87%E0%B8%82%E0%B9%89%E0%B8%AD%E0%B8%A1%E0%B8%B9%E0%B8%A5%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%9B%E0%B8%8F%E0%B8%B4%E0%B9%80%E0%B8%AA%E0%B8%98%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%82%E0%B8%B6%E0%B9%89%E0%B8%99%E0%B9%80%E0%B8%84%E0%B8%A3%E0%B8%B7%E0%B9%88%E0%B8%AD%E0%B8%87-denied-boardings-%E0%B9%83%E0%B8%99%E0%B8%AD%E0%B9%80%E0%B8%A1%E0%B8%A3%E0%B8%B4%E0%B8%81%E0%B8%B2-8fcc0b0f7c05)

ในโพสต์ของพี่ต้าค่อนข้างอธิบายรายละเอียดเกือบทั้งหมดแล้วเกี่ยวกับหน้าตาของข้อมูล เราจะไม่อธิบายในที่นี้
แค่จะมาลอง clean data แบบสบายๆและพล็อตเป็นหลัก

## ข้อมูล

ก่อนอื่นต้องเกริ่นว่า code ที่ใช้ในการเก็บ data และพล็อตอยู่บน Github repository ซึ่งใช้ชื่อว่า
[tupleblog/tuple_code](https://github.com/tupleblog/tuple_code/tree/master/denied_boarding)
ในโพสต์หน้าๆ เราจะเริ่มเอาโค้ดมาใส่ใน [tuple_code](https://github.com/tupleblog/tuple_code/) เป็นหลัก

ก่อนจะลองกันเราโหลด CSV file มาก่อนจาก repository เลย ชื่อไฟล์ว่า `denied_boarding.csv`
ส่วนที่มาของ data นั้น เราใช้ `BeaitifulSoup` ในการเก็บข้อมูลมาจากเว็บ [Bureau of Transportation Statistics](https://www.rita.dot.gov/bts/sites/rita.dot.gov.bts/files/subject_areas/airline_information/passengers_denied_confirmed_space_report/2016/index.html) ส่วนสคริปที่ใช้เก็บข้อมูลชื่อว่า [download_report.py](https://github.com/tupleblog/tuple_code/blob/master/denied_boarding/download_report.py)
ลองไปดูรายละเอียดกันได้


## Bokeh plot

ก่อนอื่นเลยเราจะมาโหลด data ไปในรูปของ dataframe ก่อนโดยใช้ไลบรารี่ชื่อว่า `pandas`
ในแต่ละหลักของข้อมูลยังไม่ได้เป็น `int` จึงต้องลบ `,` กับ `$` เล็กน้อยโดยใช้ `replace`
หน้าตาของโค้ดเป็นไปตามด้านล่าง

```py
import pandas as pd

df = pd.read_csv("denied_boarding.csv")
for col in ['3', '4', '7', '8(a)']:
    df.loc[:, col] = df[col].map(lambda x: int(x.replace('$', '').replace(',', '')))
df.loc[:, 'denied_boarding_ratio'] = df['4']/df['7'] * 10000 # denied boarding ratio
```

หลังจากนั้น เราได้ dataframe ที่พร้อมใช้พล็อตกันละ เรามาพล็อตกันสองอย่าง

### Bar Chart

ก่อนอื่นต้องโหลด function ที่เราจะใช้กันจาก `bokeh` ไลบรารี่

```py
from bokeh.models import HoverTool, ColumnDataSource, LabelSet
from bokeh.charts import Bar, Scatter
from bokeh.resources import CDN
from bokeh.embed import file_html
```

จากนั้น เนื่องจากเรามีข้อมูลจากหลาย quarter เราจะเลือกมาพล็อตเพียงแค่ quarter เดียวก่อน ใช้คำสั่ง
`df.query(...)` ในที่นี้เราจะพล็อต quarter สุดท้ายของปี 2016 กัน

```py
# bar plot
quarter = '2016_4q'
df_q = df.query("time=='%s'" % quarter).sort_values('denied_boarding_ratio', ascending=False)

hover = HoverTool(
        tooltips=[
            ('Airline', "@CARRIER"),
            ("denied boarding ratio", "@height")
        ]
)
p = Bar(df_q,
        label='CARRIER',
        values='denied_boarding_ratio',
        color="wheat",
        tools=[hover], title="Denied Boarding " + quarter, legend=None,
        xlabel="Airlines", ylabel="Number of passengers denied boarding/10000 Passengers")

html = file_html(p, CDN, "my plot")
output_file = open('bar_plot.html', 'w')
output_file.write(html)
```

<figure><center>
  <img width="auto" src="/images/post/denied_boarding/denied_boarding_ratio.png" data-action="zoom"/>
</center></figure>


## Scatter Plot


```py
# scatter plot
quarter = '2016_4q'
df_q = df.query("time=='%s'" % quarter).sort_values('3', ascending=False)
hover = HoverTool(
        tooltips=[
            ('Denied boarding involuntarily', "$x"),
            ("Total Boarding", "$y")
        ]
)
labels = LabelSet(x='3', y='7', text='CARRIER',
                  x_offset=5, y_offset=-5, source=ColumnDataSource(df_q),
                  text_font_size="8pt", text_color="#555555")

ps = Scatter(df_q, x='3', y='7',
             title="Denied Boarding vs. Total Boarding in " + quarter,
             xlabel="Denied boarding involuntarily",
             ylabel="Total Boarding",
             tools=[hover],
             )
ps.add_layout(labels)
html = file_html(ps, CDN, "my plot")
output_file = open('scatter_plot.html', 'w')
output_file.write(html)
```

<figure><center>
  <img width="auto" src="/images/post/denied_boarding/boarding_denied_scatter.png" data-action="zoom"/>
</center></figure>


ใครที่ตามไม่ทันในโพสต์นี้หรืออยากลองไปทำกันต่อ สามารถไปดูกันได้ที่ [`bokeh_plot.ipynb`](https://github.com/tupleblog/tuple_code/blob/master/denied_boarding/bokeh_plot.ipynb)