Analyzing ACL Tear Data to Discover Demographic, Injury, Diagnosis, and Information Trends

Marisa Ricci and Matthew Scherp

Hyperlink to github webpage: https://github.com/MatthewBubble/MatthewBubble.github.io/

Project Description

Our project is an analysis of data relating to ACL tears. Both of us play soccer and ACL tears are incredibly common so we wanted to do an analyis to find correlations between different variables and ACL tears as well as interesting post ACL tear data. We found data sets that provided us data on people who have torn ACLs including tear mechanisms, diagnosistic tools used (and their conclusions), and demographic information. We also found information on how people are informed and beliefs they hold about them.

Collaboration Plan

We are in every class together and also work the same on campus job. Currently, we plan to meet on each other's shifts, for at least 2-3 hours a week, but up to 8 if needed. We have set up our GitHub repository to share information and are sharing a Colab notebook to update coding for our dataset. We also each have copies of the Colab notebooks and can commit the changes to the master which reduces the possibility of changes getting overwritten if we are working at the same time. We also have each picked a data set to be the "expert" in and as we had more data sets we will likely continue to specialize. However, we are checking in with each other and giving the other person suggestions for other interesting elements to work on in their data.

Project Goals

By the end of this project we want to have found several statistical relationships in our ACL data in the following categories: diagnosis, demographics, injury mechanism, and level of information. While we can't draw any causation related conclusions from statistical correlations, we can see potential avenues for further research.

Potential Questions to Explore

1) How accurate are physicians in diagnosing ACL tears from their own physical exmainations, MRIs, and other relevant tests?

2) Do demographics such as age and gender play a role in likelihood of an ACL tear?

3) How do people commonly hear about ACL tears? Is there a lot of misinformation surrounding ACL tears?

4) What are common causes of ACL tears and what motions are correlated to ACL tears?

DATA SETS/WORKS CITED

Schmidt, Gregory (2021). Raw ACL Data.xlsx. figshare. Dataset. https://doi.org/10.6084/m9.figshare.16620421.v1

Nagano, Yasuharu (2017). raw_data.csv. Dataset. https://zenodo.org/records/844853


[ ]
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import string as str
import seaborn as sns

[ ]
from google.colab import drive
drive.mount('/content/drive')
%cd /content/drive/MyDrive/'Colab Notebooks'
Mounted at /content/drive
/content/drive/MyDrive/Colab Notebooks
This first dataset (labeled "ACL2"), includes a lot of relevant information regarding the patients and their injuries, such as previous surgeries, the cause of injury, age, sex, knee, and severity of the injury. With this dataset, we hope to answer some questions about what types of movements and/or sports cause the most ACL tears. Additionally, do contact and other factors outside of exclusive movement create other additional risks to tearing the ACL, maybe even age or gender?


[ ]
# import data
ACL2 = pd.read_excel('../Project/RawACLData.xlsx')
# drop blank columns with no data
ACL2 = ACL2.drop(columns=['Unnamed: 0', 'Unnamed: 1','Column1', 'Column2',
                          'Column3', 'Column4', 'Column5', 'Column6', 'Number'])
# drop all rows past 88 (last row with usable data because rows below might
# have 2-5 columns filled out of the 29)
ACL2 = ACL2.reset_index(drop=True)
ACL2 = ACL2.iloc[:88]
# rename columns
ACL2 = ACL2.rename(columns={'injured knee (R or L)': 'Injured Knee',
                            'Lachman injured (pos=1, neg=0)':'Lachman injured',
                            'Diagnosis by Physician (Y=1 or N=0)':'Diagnosis by Physician',
                            'ACL Arthroscopy (1 = torn ACL or 0 = intact ACL)':'ACL Arthroscopy',
                            'Effusion (1 or 0)':'Effusion',
                            'MRI (1 or 0)':'MRI',
                            'ACL MRI (1 = torn ACL or 0 = ACL intact)':'ACL MRI',
                            'OR (1 or 0)':'ACL or Meniscus',
                            'Arthroscopy (1 = completely confirmed MRI or 0 = did not confirm)':'Arthroscopy confirmed MRI',
                            'ACL Arthroscopy (1 = torn ACL or 0 = intact ACL)': 'Torn ACL from Arthroscopy'})
ACL2


[ ]
# demographic dataframe
demo_ACL2_df = ACL2[['Age', 'sex', 'Injured Knee', 'Torn ACL from Arthroscopy']]
demo_ACL2_df
# We can find most similar people/injuries from this


[ ]
ACL2.dtypes


[ ]
# count females with potentially torn ACLs
female_acl_count = demo_ACL2_df[demo_ACL2_df["sex"] == 'F'].shape[0]

# count males with potentially torn ACLs
male_acl_count = demo_ACL2_df[demo_ACL2_df["sex"] == 'M'].shape[0]

# print in a pretty way
print(f"Females with torn ACLs: {female_acl_count}")
print(f"Males with torn ACLs: {male_acl_count}")
Females with torn ACLs: 39
Males with torn ACLs: 49

[ ]
# causes of potential ACL tear
causes_df = ACL2[["mechanism of injury"]]

# new column category based on mechanism of injury
causes_df['category'] = causes_df['mechanism of injury']

# assign category to rows where the mechanism of injury contains the specific row
# nothing assigned twice and specific events (e.g. soccer, vehicle accident)
# are preferenced over general motions (e.g. twisting, standing)
# Some things were also not standard such as pivot vs pivoting so those needed
# to be put in one category.

# general categories
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Tackled', case=False, na=False), 'category'] = 'Tackled/Collision'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Collision', case=False, na=False), 'category'] = 'Tackled/Collision'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'twisting', case=False, na=False), 'category'] = 'Twisting'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Fall', case=False, na=False), 'category'] = 'Fall'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'pivot', case=False, na=False), 'category'] = 'Pivoting'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Landing', case=False, na=False), 'category'] = 'Landing'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Standing', case=False, na=False), 'category'] = 'Standing'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Hyperextension', case=False, na=False), 'category'] = 'Hyperextension'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Hypextension', case=False, na=False), 'category'] = 'Hyperextension'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Hyperextended', case=False, na=False), 'category'] = 'Hyperextension'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'No inciting event', case=False, na=False), 'category'] = 'No Inciting Event'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Nothing specific', case=False, na=False), 'category'] = 'No Inciting Event'

# more specific categories (will overwrite general categories if specificity found)
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Soccer', case=False, na=False), 'category'] = 'Soccer'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Football', case=False, na=False), 'category'] = 'Football'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Footabll', case=False, na=False), 'category'] = 'Football'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Basketball', case=False, na=False), 'category'] = 'Basketball'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Volleyball', case=False, na=False), 'category'] = 'Volleyball'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Crossfit', case=False, na=False), 'category'] = 'Crossfit'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Dancing', case=False, na=False), 'category'] = 'Dancing'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Ultimate Frisbee', case=False, na=False), 'category'] = 'Ultimate Frisbee'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'MVA', case=False, na=False), 'category'] = 'Vehicle Accident'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Motorcycle Accident', case=False, na=False), 'category'] = 'Vehicle Accident'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Baseball', case=False, na=False), 'category'] = 'Baseball/Softball'
causes_df.loc[causes_df['mechanism of injury'].str.contains(
    'Softball', case=False, na=False), 'category'] = 'Baseball/Softball'

# count occurrences of each category
category_counts = causes_df['category'].value_counts()

# plot counts
plt.figure(figsize=(10, 6))
sns.barplot(x=category_counts.index, y=category_counts.values)

# plot details
plt.title('Quantity of People vs Category of Injury', fontsize=16)
plt.xlabel('Injury Category', fontsize=14)
plt.ylabel('Number of People', fontsize=14)
plt.xticks(rotation=45, ha='right')

Interestingly, no specific event of an ACL tear is the most common, whereas the following two sports are basketball and soccer. Additionally, a cause we had never considered in creating ACL tears would be vehicle accidents. Broken limbs and wounds are common but ACL tears from vehicle accidents are not usually protrayed in the media which is where many of our assumptions about the injuries related to vehicle accidents come from.


[ ]
# accuracy of diagnosis
diagnosis_df = ACL2[["Lachman injured",
                     "Diagnosis by Physician",
                     "ACL MRI",
                     'Torn ACL from Arthroscopy']]
# These are all the different ways the study attemped to diagnose an ACL tear
# with ACL anthroscopy being the last (actually surgical where you look and see
# if the ACL is torn). We can use this dataframe to compare the accuracy of
# Lachman tests, physician diagnoses, and ACL MRIs.

diagnosis_df

We were able to collect a questionnaire dataset that is relevant to the awareness of ACL tears and what people believe are factors causing these tears. We think this will be a good dataset to answer some questions about how/where people are finding out about ACL tears and how common and severe it is. Additionally, it can also show what people think are factors causing these ACL tears and what we can potentially do to help get more people proactive in trying to prevent an ACL tear by either encouraging people to strengthen those factors or making more people aware of other factors they do not see as significant.


[ ]
ACL3 = pd.read_csv("../Project/raw_data.csv",encoding = "latin-1")
#Removing first row, and renaming all of the columns into names that are
#recognizable and short for ease of calling
ACL3 = ACL3[1:].astype(int)
ACL3.columns = ["UserID", "ACL", "Self Injury", "Injured Family/Friend","Friends","Family",
                "Coach","Health Class", "Other Class","TV", "Magazine",
                "Comic","Internet", "Newpaper","Poster","Other","Bone Geometry",
                "ACL Size","Joint Laxity","Hormone","Flexibility","Foot Pronation",
                "Weak Quadricep Muscle", "Weak Hamstring Muscle","Weak Hip Muscle","Balance",
                "Weight","Drinking","Smoking","Genu Valgum","Genu Varum",
                "Sex","Age"]
#Making Male and Female easily read on the dataset
ACL3["Sex"] = ACL3["Sex"].map({
    0:"M",
    1:"F"
})

ACL3.dtypes


[ ]
#Taking all of the values from the first question and better formating it
ACL3_Discover = ACL3[["UserID", "ACL", "Self Injury", "Injured Family/Friend",
                      "Friends","Family","Coach","Health Class", "Other Class",
                      "TV", "Magazine", "Comic","Internet", "Newpaper","Poster",
                      "Other","Sex","Age"]].copy()
#Printing to show the dataset
print(ACL3_Discover)
#Taking the percentage of each value
discover_ave = ACL3_Discover[["Self Injury", "Injured Family/Friend","Friends","Family",
                 "Coach","Health Class", "Other Class","TV", "Magazine", "Comic",
                 "Internet", "Newpaper","Poster",
                              "Other"]].sum()/len(ACL3_Discover) * 100
#Resorting from highest to lowest
discover_ave = discover_ave.sort_values(ascending = False)
#Plotting the values
ax = discover_ave.plot.bar()
ax = plt.title("Informed of ACL Injury by Sources")
ax = plt.ylabel("Percentage of people informed by that source (%)")
ax = plt.xlabel("Source")
#Small note as people posted more than one answer, making the percentage higher
#than 100%
print("NOTE: % does not equal to 100% as some people have been informed by " +
      "multiple sources")

When looking at those statistics on the Colab, TV was the biggest source of ACL tear awareness, with friends and coaches following. This is interesting, but not super surprising, as many top-tier athletes tear their ACL due to high strain on their body and that is broadcasted all over television.


[ ]
#Taking all of the values from the second question and better formating it
ACL3_Fac = ACL3[["UserID","Bone Geometry","ACL Size","Joint Laxity",
                    "Hormone","Flexibility","Foot Pronation","Weak Quadricep Muscle",
                    "Weak Hamstring Muscle","Weak Hip Muscle","Balance","Weight",
                    "Drinking","Smoking","Genu Valgum","Genu Varum","Sex","Age"]]
#Fixing the 2 into false, like the previous values
#ACL3_Fac =ACL3_Fac.replace(1,1) Not needed code, but kept for if needed
ACL3_Fac =ACL3_Fac.replace(2,0)
#Prints dataset
print(ACL3_Fac.head(5))
#Takes percentage of each factor considered
factor_ave = ACL3_Fac[["Bone Geometry","ACL Size","Joint Laxity",
                    "Hormone","Flexibility","Foot Pronation","Weak Quadricep Muscle",
                    "Weak Hamstring Muscle","Weak Hip Muscle","Balance","Weight",
                    "Drinking","Smoking","Genu Valgum","Genu Varum"]].sum()/len(ACL3_Fac) *100
#Resorts the values highest to lowest
factor_ave = factor_ave.sort_values(ascending = False)
#Plotting the values
ax = factor_ave.plot.bar()
ax = plt.title("% of people that believe is a factor for ACL injuries")
ax = plt.ylabel("% of people believe is a factor")
ax = plt.xlabel("Factor")


As for factors considered in ACL tears, weight and flexibility were among the highest. These are not very surprising as flexibility and less weight strain both definitely help ligament movement and preventative tears.

