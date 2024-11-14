# Text to file Project

```python
import pandas as pd
from collections import defaultdict
import plotly.express as px
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import nltk

df = pd.read_csv(r'Customer_Feedback.csv', sep=",", quotechar='"', on_bad_lines='skip')
print("Column Names in DataFrame:")
print(df.columns)
print("First few rows of the DataFrame:")
print(df.head())

file_path = 'feedback.txt'

line_count = 0

with open(file_path, 'r') as file:
    for line in file:
        line_count += 1  

print(f'Total number of feedback entries: {line_count}')

keywords = ["good", "bad", "excellent", "poor"]

feedback_keywords = []

with open(file_path, 'r') as file:
    for line_num, line in enumerate(file, 1):
        
        feedback_lower = line.lower()
        
        found_keywords = set()
        
        for keyword in keywords:
            if keyword in feedback_lower:
                found_keywords.add(keyword)
        if found_keywords:
            feedback_keywords.append((line_num, list(found_keywords)))

for entry in feedback_keywords:
    line_num, found = entry
    print(f"Feedback Entry {line_num}: Keywords found - {', '.join(found)}")

keyword_count = defaultdict(int)
with open(file_path, 'r') as file:
    for line in file:
        feedback_lower = line.lower()

        for keyword in keywords:
            keyword_count[keyword] += feedback_lower.count(keyword)

for keyword in keywords:
    print(f"'{keyword}': {keyword_count[keyword]} occurrences")

keyword_list = list(keyword_count.keys())
count_list = list(keyword_count.values())

analyzer = SentimentIntensityAnalyzer()

def get_sentiment(text):
    sentiment_dict = analyzer.polarity_scores(text)
    compound = sentiment_dict['compound']
    
    accuracy = (compound) * 100
    
    if compound >= 0.30:
        sentiment = 'Positive'
    elif compound < 0.30:
        sentiment = 'Negative'
    else:
        sentiment = 'Neutral'
        
    return sentiment, accuracy

with open('feedback.txt', 'r') as file:
    feedback_list = file.readlines()

feedback_sentiment = []
for feedback in feedback_list:
    sentiment, accuracy = get_sentiment(feedback)
    feedback_sentiment.append({'Feedback': feedback.strip(), 'Sentiment': sentiment, 'Accuracy (%)': round(accuracy, 2)})

pd.set_option('display.max_rows', None)  
pd.set_option('display.max_columns', None) 

df = pd.DataFrame(feedback_sentiment)
print(df)


df.to_csv('feedback_sentiment_analysis.csv', index=False)

sentiment_counts = df['Sentiment'].value_counts().reset_index()
sentiment_counts.columns = ['Sentiment', 'Count']

fig = px.bar(sentiment_counts, x='Sentiment', y='Count', 
             color='Sentiment', 
             title='Sentiment Analysis of Feedback',
             labels={'Count': 'Number of Feedbacks'},
             color_discrete_map={'Positive': 'green', 'Negative': 'red', 'Neutral': 'blue'})
fig.show()

fig = px.bar(
    x=keyword_list,
    y=count_list,
    labels={'x': 'Keywords', 'y': 'Occurrences'},
    title="Keyword Occurrences in Feedback Entries",
    text_auto=True)

fig.show()

keywords = ["good", "bad", "excellent", "poor"]

keyword_count = defaultdict(int)
total_entries = 0

with open(file_path, 'r') as file:
    for line in file:
        total_entries += 1 
        
        feedback_lower = line.lower()

        for keyword in keywords:
            keyword_count[keyword] += feedback_lower.count(keyword)          
print("Summary Report:")
print(f"Total Number of Feedback Entries: {total_entries}\n")

print("Keyword Occurrences:")
for keyword in keywords:
    print(f"  '{keyword}': {keyword_count[keyword]} occurrences")
with open('summary_report.txt', 'w') as report_file:
    report_file.write("Summary Report:\n")
    report_file.write(f"Total Number of Feedback Entries: {total_entries}\n\n")
    report_file.write(f"Sentiment_Analysis: {feedback_sentiment}\n\n")
    report_file.write("Keyword Occurrences:\n")
    for keyword in keywords:
        report_file.write(f"  '{keyword}': {keyword_count[keyword]} occurrences\n")

print("\nSummary report has been saved to 'summary_report.txt'.")
```

## Output File
```bash
Column Names in DataFrame:
Index(['Messages'], dtype='object')
First few rows of the DataFrame:
                                            Messages
0  "Great,service!,The,staff,was,very,helpful,and...
1  "I,had,a,bad,experience,with,the,customer,supp...
2  "The,new,update,is,excellent,and,improves,"usa...
3  "Received,a,defective,product.,The,quality,is,...
4  "Good,value,for,money.,I'm,satisfied,with,the,...
Total number of feedback entries: 110
Feedback Entry 2: Keywords found - excellent
Feedback Entry 3: Keywords found - poor, bad
Feedback Entry 4: Keywords found - excellent
Feedback Entry 5: Keywords found - poor, bad
Feedback Entry 6: Keywords found - good
Feedback Entry 7: Keywords found - excellent
Feedback Entry 8: Keywords found - good
Feedback Entry 9: Keywords found - bad
Feedback Entry 10: Keywords found - good, excellent
Feedback Entry 11: Keywords found - poor
Feedback Entry 12: Keywords found - good, excellent
Feedback Entry 13: Keywords found - poor
Feedback Entry 14: Keywords found - good
Feedback Entry 15: Keywords found - poor, bad
Feedback Entry 16: Keywords found - excellent
Feedback Entry 17: Keywords found - excellent
Feedback Entry 18: Keywords found - good, poor
Feedback Entry 19: Keywords found - bad
Feedback Entry 20: Keywords found - excellent
Feedback Entry 21: Keywords found - good, bad
Feedback Entry 22: Keywords found - excellent
Feedback Entry 23: Keywords found - good, poor
Feedback Entry 24: Keywords found - excellent
Feedback Entry 25: Keywords found - good
Feedback Entry 26: Keywords found - poor
Feedback Entry 27: Keywords found - good
Feedback Entry 28: Keywords found - good, poor
Feedback Entry 29: Keywords found - excellent
Feedback Entry 30: Keywords found - bad
Feedback Entry 31: Keywords found - good
Feedback Entry 32: Keywords found - excellent
Feedback Entry 33: Keywords found - good, poor
Feedback Entry 34: Keywords found - good
Feedback Entry 35: Keywords found - excellent
Feedback Entry 36: Keywords found - bad
Feedback Entry 37: Keywords found - excellent
Feedback Entry 38: Keywords found - bad
Feedback Entry 39: Keywords found - poor
Feedback Entry 40: Keywords found - good, bad
Feedback Entry 41: Keywords found - excellent
Feedback Entry 42: Keywords found - good
Feedback Entry 43: Keywords found - excellent
Feedback Entry 44: Keywords found - good, poor
Feedback Entry 45: Keywords found - good, bad
Feedback Entry 46: Keywords found - excellent
Feedback Entry 47: Keywords found - poor
Feedback Entry 48: Keywords found - good, poor
Feedback Entry 49: Keywords found - excellent
Feedback Entry 50: Keywords found - good
Feedback Entry 51: Keywords found - poor
Feedback Entry 52: Keywords found - good, poor
Feedback Entry 53: Keywords found - excellent
Feedback Entry 54: Keywords found - good
Feedback Entry 55: Keywords found - bad
Feedback Entry 56: Keywords found - excellent
Feedback Entry 57: Keywords found - good, poor
Feedback Entry 58: Keywords found - excellent
Feedback Entry 59: Keywords found - good, poor
Feedback Entry 60: Keywords found - bad, excellent
Feedback Entry 61: Keywords found - good
Feedback Entry 62: Keywords found - excellent
Feedback Entry 63: Keywords found - poor
Feedback Entry 64: Keywords found - good
Feedback Entry 65: Keywords found - bad
Feedback Entry 66: Keywords found - excellent
Feedback Entry 67: Keywords found - good, poor
Feedback Entry 68: Keywords found - good, bad
Feedback Entry 69: Keywords found - excellent
Feedback Entry 70: Keywords found - bad
Feedback Entry 71: Keywords found - excellent
Feedback Entry 72: Keywords found - good
Feedback Entry 73: Keywords found - poor
Feedback Entry 74: Keywords found - excellent
Feedback Entry 75: Keywords found - good
Feedback Entry 76: Keywords found - excellent
Feedback Entry 77: Keywords found - poor
Feedback Entry 78: Keywords found - good
Feedback Entry 79: Keywords found - poor
Feedback Entry 80: Keywords found - good
Feedback Entry 81: Keywords found - excellent
Feedback Entry 82: Keywords found - bad
Feedback Entry 83: Keywords found - bad
Feedback Entry 84: Keywords found - excellent
Feedback Entry 85: Keywords found - good
Feedback Entry 86: Keywords found - poor
Feedback Entry 87: Keywords found - excellent
Feedback Entry 88: Keywords found - poor
Feedback Entry 89: Keywords found - excellent
Feedback Entry 90: Keywords found - good, bad
Feedback Entry 91: Keywords found - excellent
Feedback Entry 92: Keywords found - good
Feedback Entry 93: Keywords found - good
Feedback Entry 94: Keywords found - good, bad
Feedback Entry 95: Keywords found - excellent
Feedback Entry 96: Keywords found - bad
Feedback Entry 97: Keywords found - good, poor
Feedback Entry 98: Keywords found - good
Feedback Entry 99: Keywords found - excellent
Feedback Entry 100: Keywords found - poor
Feedback Entry 101: Keywords found - good
Feedback Entry 102: Keywords found - good, bad
Feedback Entry 103: Keywords found - excellent
Feedback Entry 104: Keywords found - good, poor
Feedback Entry 105: Keywords found - excellent
Feedback Entry 106: Keywords found - good, poor
Feedback Entry 107: Keywords found - good
Feedback Entry 108: Keywords found - bad
Feedback Entry 109: Keywords found - excellent
Feedback Entry 110: Keywords found - poor
'good': 44 occurrences
'bad': 23 occurrences
'excellent': 38 occurrences
'poor': 30 occurrences
                                              Feedback Sentiment  Accuracy (%)
0                                             Messages  Negative          0.00
1    "Great service! The staff was very helpful and...  Positive         90.39
2    "I had a bad experience with the customer supp...  Negative        -59.94
3    "The new update is excellent and improves usab...  Positive         75.79
4    "Received a defective product. The quality is ...  Negative        -85.91
5    "Good value for money. I'm satisfied with the ...  Positive         79.64
6      "Excellent customer service and fast delivery."  Positive         57.19
7    "The product is good, but the delivery took to...  Negative         23.82
8    "Very bad experience. I will not shop here aga...  Negative        -58.49
9    "Good quality product and excellent customer s...  Positive         85.19
10   "The service was poor and the product did not ...  Negative        -47.67
11   "The service was good and the staff was excell...  Positive         76.50
12                     "The product quality was poor."  Negative        -47.67
13                  "I had a good experience overall."  Positive         44.04
14   "The delivery time was bad and the packaging w...  Negative        -76.50
15   "The support team was excellent and very helpf...  Positive         85.88
16   "The support team was excellent and very helpf...  Positive         85.88
17   "The interface is good, but the performance is...  Negative        -49.39
18   "My experience with the customer service was b...  Negative        -54.23
19   "I received excellent assistance from the supp...  Positive         75.06
20   "The quality of the product was good, but the ...  Negative        -58.59
21   "The transaction process was excellent, and I ...  Positive         77.78
22   "The website design is good, but the navigatio...  Negative        -49.39
23   "The response time from the support team was e...  Positive         75.06
24   "The delivery was on time and the packaging wa...  Positive         44.04
25   "I had a poor experience with the customer sup...  Negative        -10.27
26   "The product met my expectations and the quali...  Positive         44.04
27   "The staff was good, but the wait time was poor."  Negative        -49.39
28   "The app interface is excellent and user-frien...  Positive         57.19
29   "I had a bad experience with the return process."  Negative        -54.23
30   "The support team was good and resolved my iss...  Positive         74.30
31   "The product quality was excellent and I am ve...  Positive         82.68
32   "The delivery time was poor, but the product q...  Positive         42.15
33   "I had a good experience with the customer ser...  Positive         44.04
34   "The transaction was smooth and the service wa...  Positive         57.19
35       "The product was bad and I had to return it."  Negative        -54.23
36   "The packaging was excellent and the product w...  Positive         57.19
37   "The support was bad and took too long to resp...  Negative        -20.23
38   "I had a poor experience with the online order...  Negative        -47.67
39   "The product quality is good, but the delivery...  Negative        -58.59
40    "The staff was excellent and very professional."  Positive         57.19
41       "The user experience on the website is good."  Positive         44.04
42   "The product quality was excellent and I am ve...  Positive         77.78
43   "The product quality was good, but the custome...  Negative        -49.39
44   "The delivery was bad, but the packaging was g...  Positive         38.18
45   "The support team was excellent and resolved m...  Positive         79.64
46   "The product was poor and did not meet my expe...  Negative        -47.67
47   "The website design is good, but the checkout ...  Negative        -49.39
48   "The service was excellent and very profession...  Positive         57.19
49   "The product quality was good, but the shippin...  Negative         23.82
50      "The customer service was poor and unhelpful."  Negative        -47.67
51   "The support team was good, but the response t...  Negative        -32.91
52    "The product was excellent and arrived on time."  Positive         57.19
53   "The packaging was good and the product was un...  Positive         44.04
54   "The service was bad and I am very disappointed."  Negative        -78.41
55   "The product quality was excellent and worth t...  Positive         68.08
56   "The delivery was good, but the packaging was ...  Negative        -49.39
57   "The customer service was excellent and very h...  Positive         77.78
58   "The support team was good, but the wait time ...  Negative        -32.91
59   "The service was excellent and very profession...  Negative          5.16
60   "The product was good, but the price was too h...  Negative         23.82
61   "I had an excellent experience with the suppor...  Positive         75.06
62   "The product quality is poor and not as advert...  Negative        -47.67
63   "The customer service was good and resolved my...  Positive         55.74
64   "The delivery was bad and delayed by several d...  Negative        -65.97
65   "The product was excellent and exceeded my exp...  Positive         57.19
66   "The support team was good, but the wait time ...  Negative        -32.91
67   "The website interface is good, but the loadin...  Negative        -58.59
68   "The transaction process was smooth and excell...  Positive         57.19
69   "I had a bad experience with the product quali...  Negative        -54.23
70   "The support was excellent and resolved my iss...  Positive         79.64
71   "The product quality is good, but the price is...  Negative         23.82
72   "The delivery was poor and the package was dam...  Negative        -71.84
73   "I had an excellent experience with the custom...  Positive         57.19
74            "The product was good and as described."  Positive         44.04
75         "The staff was excellent and very helpful."  Positive         77.78
76   "The packaging was poor and the product was da...  Negative        -71.84
77   "The support team was good and assisted me well."  Positive         77.17
78   "The quality of the product was poor and disap...  Negative        -74.30
79   "I had a good experience with the return proce...  Positive         44.04
80    "The product was excellent and arrived on time."  Positive         57.19
81       "The customer service was bad and unhelpful."  Negative        -54.23
82       "The customer service was bad and unhelpful."  Negative        -54.23
83   "The website design is excellent and very intu...  Positive         57.19
84   "The product was good, but the shipping was sl...  Negative         23.82
85    "I had a poor experience with the support team."  Negative        -10.27
86         "The quality of the service was excellent."  Positive         57.19
87     "The product was poor and not worth the price."  Negative        -58.12
88         "The delivery was excellent and very fast."  Positive         57.19
89   "The customer service was good, but the produc...  Negative        -58.59
90   "The support team was excellent and very knowl...  Positive         75.06
91   "The packaging was good, but the product was d...  Negative        -44.04
92         "I had a good experience with the website."  Positive         44.04
93   "The delivery time was bad, but the product wa...  Positive         38.18
94   "The customer service was excellent and very r...  Positive         75.74
95   "The product was bad and I am very disappointed."  Negative        -78.41
96   "The packaging was poor, but the product was g...  Positive         42.15
97   "The support was good, but the wait time was t...  Positive         42.15
98   "I had an excellent experience with the service."  Positive         57.19
99      "The product was poor and I had to return it."  Negative        -47.67
100  "The support team was good and assisted me well."  Positive         77.17
101  "The quality of the product was good, but the ...  Negative        -58.59
102  "The service was excellent and met my expectat...  Positive         57.19
103  "The product was good, but the support was poor."  Negative          9.00
104     "The packaging was excellent and very secure."  Positive         75.01
105  "The delivery time was poor, but the product q...  Positive         42.15
106  "The customer service was good and resolved my...  Positive         55.74
107       "The product was bad and I am very unhappy."  Negative        -76.45
108  "The support team was excellent and very frien...  Positive         87.18
109  "The quality of the product was poor and disap...  Negative        -74.30
Summary Report:
Total Number of Feedback Entries: 110

Keyword Occurrences:
  'good': 44 occurrences
  'bad': 23 occurrences
  'excellent': 38 occurrences
  'poor': 30 occurrences

Summary report has been saved to 'summary_report.txt'.
```
