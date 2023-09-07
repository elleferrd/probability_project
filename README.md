# probability_project

# Introduction
Projek ini bertujuan untuk mengeksplorasi hubungan antara demografi, lifestyle dan tagihan kesehatan. Variabel yang dieksplorasi adalah usia, gender, BMI, merokok dan tagihan kesehatan.

# Research Question
Proyek eksplorasi data ini akan menjawab beberapa pertanyaan terkait demografi, gaya hidup dan tagihan kesehatan, yaitu:
1) Apakah terdapat hubungan antara usia dan berat badan?
2) Apakah perokok cenderung memiliki tagihan kesehatan lebih mahal daripada non perokok?
3) Apakah terdapat hubngan antarai BMI dam biaya tagihan kesehatan
4) Apakah terdapat hubungan antara usia dan biaya tagihan kesehatan?
5) Apakah tagihan kesehatan usia >40 lebih mahal daripada usia <40?
6) Mana yang lebih mungkin terjadi seseorang dengan usia diatas atau dibawah 40 mendapatkan tagihan lebih besar dari rata-rata?
7) Variabel apa yang memiliki hubungan paling kuat?

# Pengolahan Data

 Langkah pertama adalah import library, load data, understanding data dan data manipulation

         #import libraries
         import pandas as pd
         import numpy as np
         import libraries
         import matplotlib.pyplot as plt
         from matplotlib.pyplot import figure
         from scipy.stats import t
         from scipy.stats import f_oneway
         import seaborn as sns
         #load data
         insurance = pd.read_csv("TEST\datainsurance.csv")
         
![kategori data](https://github.com/elleferrd/probability_project/assets/137087598/20a07ac8-0932-4fc7-ad14-afd08d8eff29)

         #Data wraggling
         #Mengelompokan BMI & Usia
         insurance['BMI'] = insurance['bmi'].apply(lambda x: "Extremely Obese" if x>35 else "Obese" if x>=30 else "Overweight" if x>=25 else "Normal" if x>=18.5 else "Underweight")
         insurance['ageGroup'] = insurance['age'].apply(lambda x: "60s" if x>=60 else "50s" if x>=50 else "40s" if x>=40 else "30s" if x>=30 else "20s" if x>=20 else "10s")

 Output:
![kategori data](https://github.com/elleferrd/probability_project/assets/137087598/e992c05d-141f-4744-a6d5-c93b65b74dd4)

         
         #summarize data dengan pivot table
         umurbmi =insurance.pivot_table(values=["charges"], index = ["ageGroup"], columns=["BMI"],aggfunc='count')
         #mendeskripsikan data alah satu contoh)
         BM = insurance[['BMI', 'charges']]
         rerata = BM.groupby('BMI').mean('charges')
         rerata.columns=["rata-rata"]
         deviasi = BM.groupby('BMI').std()
         deviasi.columns=["standarDeviasi"]
         mini = BM.groupby('BMI').min()
         mini.columns=["nilaiMinimum"]
         maks = BM.groupby('BMI').max()
         maks.columns=["nilaiMaksimum"]
         sebarandata = pd.DataFrame(insurance.groupby('BMI')['charges'].size())
         sebarandata.columns =["jumlah"]
         sebarandata['proporsi'] = sebarandata['jumlah'].apply(lambda x: x/(sebarandata['jumlah'].sum()))

         #mengelompokan data
         
         #mengetahui karakteristik data
         agebmi = insurance.groupby("ageGroup")["bmi"].agg(["min", "max", "mean", "var", "std"]) #data
         #random sampling 200 data
         insurance =insurance.sample(n=200, random_state=42)
         


# Hasil
