# probability_project
Projek ini bertujuan untuk mengeksplorasi hubungan antara demografi, lifestyle dan tagihan kesehatan. Variabel yang dieksplorasi adalah usia, gender, BMI, merokok dan tagihan kesehatan.

Berdasarkan literatur, Demografi dan gaya hidup dapat mempengaruhi kesehatan seseorang. Semakin bertambahnya usia, metabolisme melambat dan sistem imun manusia melemah, sehingga lebih mudah terserang penyakit. Selain dari usia, jenis kelamin juga memiliki pengaruh terhadap kesehatan seseorang, yang mana terdapat beberapa penyakit yang lebih rentan bagi pria atau wanita. Seperti contohnya penyakit jantung, parkinson, melanoma, kanker pankreas, batu ginjal dan abdominal aortic anaerysm lebih rentan diderita oleh pria. Sedangkan, kanker rahim dan payudara diderita oleh wanita. Sebagai tambahan, statistik kesehatan di berbagai negara menunjukkan harapan hidup wanita lebih tinggi dari pria berdasarkan data historis.

Selain faktor usia dan jenis kelamin, faktor gaya hidup seperti pola diet dan kebiasaan merokok dapat menyebabkan beberapa macam penyakit. Seperti contohnya seseorang yang obesistas memiliki risiko yang lebih tinggi terserang penyakit jatung, tekanan darah tinggi, diabetes, gallstone, masalah pernapasan dan kanker. Pada saat  yang sama, perokok lebih rentan terhadap penyakin kanker, penyakit jantung, struk, paru-paru, diabetes, chronic obstructive pulmonary disease yang meliputi emphysema dan bronkitis kronis. 


# Research Question
Proyek eksplorasi data ini akan menjawab beberapa pertanyaan terkait demografi, gaya hidup dan tagihan kesehatan, yaitu:
1) Apakah terdapat hubungan antara usia dan berat badan?
2) Apakah perokok cenderung memiliki tagihan kesehatan lebih mahal daripada non perokok?
3) Apakah terdapat hubungan antara BMI dan biaya tagihan kesehatan
4) Apakah terdapat hubungan antara usia dan biaya tagihan kesehatan?
5) Apakah tagihan kesehatan usia >40 lebih mahal daripada usia <40?
6) Mana yang lebih mungkin terjadi seseorang dengan usia diatas atau dibawah 40 mendapatkan tagihan lebih besar dari rata-rata?
7) Variabel apa yang memiliki hubungan paling kuat?

# Pengolahan Data

 Langkah pertama adalah import library, load data, dan data manipulation

         #import libraries
         import pandas as pd
         import numpy as np
         import libraries
         import matplotlib.pyplot as plt
         from matplotlib.pyplot import figure
         from scipy.stats import t
         from scipy.stats import f_oneway
         from scipy.stats import ttest_ind
         from scipy import stats
         import seaborn as sns
         from scipy.stats.stats import pearsonr
         #load data
         insurance = pd.read_csv("TEST\datainsurance.csv")
         
![kategori data](https://github.com/elleferrd/probability_project/assets/137087598/20a07ac8-0932-4fc7-ad14-afd08d8eff29)

         #Data wraggling
         #Mengelompokan BMI & Usia
         insurance['BMI'] = insurance['bmi'].apply(lambda x: "Extremely Obese" if x>35 else "Obese" if x>=30 else "Overweight" if x>=25 else "Normal" if x>=18.5 else "Underweight")
         insurance['ageGroup'] = insurance['age'].apply(lambda x: "60s" if x>=60 else "50s" if x>=50 else "40s" if x>=40 else "30s" if x>=30 else "20s" if x>=20 else "10s")

 Output:
 
![kategori data](https://github.com/elleferrd/probability_project/assets/137087598/e992c05d-141f-4744-a6d5-c93b65b74dd4)

Langkah kedua adalah menghitung frekuensi kemuculan data per kategori untuk diolah kembali di excel

         #summarize data dengan pivot table (salah satu contoh)
         umurbmi =insurance.pivot_table(values=["charges"], index = ["ageGroup"], columns=["BMI"],aggfunc='count')

output:

![kategori data](https://github.com/elleferrd/probability_project/assets/137087598/2b952a26-d36c-46f9-91e7-908ce9cb3366)


Langkah ketiga adalah pemahaman data dengan cara mendeskripsikan data

         #mengetahui karakteristik data (contoh)
         agebmi = insurance.groupby("ageGroup")["bmi"].agg(["min", "max", "mean", "var", "std"]) 

 Output:
        
![kategori data](https://github.com/elleferrd/probability_project/assets/137087598/e0226e16-6448-488a-b1d1-d24ab56b926a)

Langkah keempat melakukan random sampling data yang akan diolah

         #random sampling 200 data
         insurance =insurance.sample(n=200, random_state=42)

 Langkah kelima adalah menjawab seluruh research question.

1) Apakah terdapat hubungan antara usia dan berat badan? menggunakan scatterplot & uji korelasi

         #melihat visual sebaran data (scatterplot)
         sns.scatterplot(data=insurance, x ="age", y = "bmi")
         #uji korelasi
         correlation_coefficient, p_value = pearsonr(insurance["age"], insurance["bmi"])
         rounded_correlation = round(correlation_coefficient, 3)
         rounded_p_value = round(p_value, 20)
         print("Pearson Correlation Coefficient:", rounded_correlation)
         print("P-value:", rounded_p_value)

![nomor1](https://github.com/elleferrd/probability_project/assets/137087598/6cee6fac-cd02-41a4-af63-751c2a68b4ab)
   
2) Apakah perokok cenderung memiliki tagihan kesehatan lebih mahal daripada non perokok? menggunakan scatter plot dan uji hipotesis

        #contoh uji hipotesis ho tagihan perokok = tagihan non perokok, h1 tagihan perokok < tagihan non perokok
        #ambil data numerik yang akan diuji
        smoke = insurance[insurance['smoker'] == 'yes']
        nonsmoke = insurance[insurance['smoker'] == 'no']
        smoke = smoke["charges"]
        nonsmoke = nonsmoke["charges"]
        #uji t less than
        stat, p = ttest_ind(young, old, equal_var=False, alternative='less') # eaual_var= False karena varians kedua populasi berbeda
        #Interpretasi Hasil
        print('Statistics = %.4f, p = %.4f' % (stat, p)) 

3) Apakah terdapat hubungan antara BMI dan biaya tagihan kesehatan? menggunakan cara yang digunakan pada research question 1 dan uji kausalitas (uji t) dan melakukan eksplorasi kondisi

        #eksplorasi kondisi sampel seluruhnya perokok atau non perokok
        #filter data smoker and non smoker
        test = insurance.set_index("smoker")
        test2 = test.loc[['yes']]
        test3 = test.loc[['no']]
        #contoh uji hipotesis ho tagihan perokok = tagihan non perokok, h1 tagihan perokok < tagihan non perokok
        stats.ttest_ind(insurance["age"], insurance["charges"])
        stats.ttest_ind(test2["age"], test2["charges"])
        stats.ttest_ind(test3["age"], test3["charges"])
  
4) Apakah terdapat hubungan antara usia dan biaya tagihan kesehatan? menggunakan cara yang digunakan pada research question nomor 3
  
5) Apakah tagihan kesehatan usia >40 lebih mahal daripada usia <40? menggunakan uji hipotesis yang dilakukan pada research question nomor 2
   
6) Mana yang lebih mungkin terjadi seseorang dengan usia diatas atau dibawah 40 mendapatkan tagihan lebih besar dari rata-rata?

   
          #menghitung rata-rata
          rerata=insurance['charges'].mean()
          #menghitung jumlah yang diatas rata-rata untuk usia <40 dan >40
          count = 0
          count2 = 0
          for item in young:
             if item>rerata:
                 count += 1
          probabilitas_young =count/young.count() 
         for item in old:
             if item>rerata:
                 count2 += 1
          probabilitas_old = count2/old.count()
          probabilitas_young, probabilitas_old
          prob = {
              'Kelompok': ['<=40 tahun', '>40 tahun'],
              'jumlah yang tagihannya diatas rata-rata': [count, count2],
              'jumlah data': [young.count(), old.count()],
              'probabilitas tagihan diatas rata-rata': [probabilitas_young, probabilitas_old]
          }
      
8) Variabel apa yang memiliki hubungan paling kuat? menggunakan heatmap korelasi
    
       forheatmap = insurance[['age', 'bmi', 'charges']]
       corr = forheatmap.corr(method="pearson")
       sns.heatmap(corr)

![gambar1](https://github.com/elleferrd/probability_project/assets/137087598/69c4eacc-65ee-4cc8-9957-bebf5fc8151f)


# Hasil
1) Usia memiliki hubungan yang lemah dengan bmi (all data)
2) Merokok memiliki hubungan dengan tagihan kesehatan dimana perokok memiliki tagihan kesehatan lebih mahal daripada non perokok. Hasil uji hipotesis
H0: Tagihan kesehatan perokok sama dengan tagihan kesehatan non perokok
H1: Tagihan kesehatan perokok lebih kecil dari tagihan kesehatan non perokok
menunjukan hasil yang signikan dengan nilai t statistik sebesar -4,612 dan pvalue sebesar 0,00 yaitu dibawah alpha 0,05.
3) Terdapat hubungan yang kuat antara bmi dan biaya tagihan kesehatan apabila diketahui sampel adalah perokok, semakin tinggi BMI seorang perokok maka semakin mahal biaya tagihan kesehatan. Namun, hubungan bmi dengan biaya tagihan tergolong sangat lemah untuk non-perokok. Meskipun demikian, diketahui bahwa bmi memiliki pengaruh terhadap tagihan kesehatan yang ditunjukan oleh p value pada uji kausalitas yaiu 0,00 dibawah alpha sebesar 0,05.
4) Terdapat hubungan anatra usia dan biaya tagihan kesehatan, yang mana semakin tinggi tingkat usia maka semakin mahal pula biaya tagihan kesehatan. Hal tersebut ditunjukan oleh hasil uji hipotesis dari:
H0: Tagihan kesehatan usia dibawah 40 sama dengan tagihan kesehatan usia diatas 40
H1: Tagihan kesehatan usia dibawah 40 lebih kecil dari tagihan kesehatan dibatas 40
Yang mana  nilai t statistik adalah sebesar -4,6125 dengan nilai p value sebesar 0,00 dibawah alpha sebesar 0,05.
5) Apakah tagihan kesehatan usia >40 lebih mahal daripada usia <40?
6) Seseorang dengan usia diatas 40 tahun lebih mungkin memiliki tagihan diatas rata-rata daripada yang dibawah 40 tahun. Hal ini ditunjukan oleh probabilitas usia diatas 40 tahun lebih mungkin memiliki tagihan diatas rata-rata  adalah 40%, sedangkan untuk usia dibawah 40 tahun adalah 22%. (tagihan rata-rata dari 200 sampel uang diambil adalah 13.236).
7) Umur dan tagihan kesehatan memiliki hubungan paling kuat. Hal tersebut ditunjukan oleh heat map korelasi yang paling terang dibanding kombinasi variabel lainnya.
