# R-Textual-Analysis
文本分析範例

## 範例文章來源網址
[美國在台協會 -- 獨立宣言（封存區）](https://web-archive-2017.ait.org.tw/zh/declaration-of-independence.html)

## 進行文本分析可初步分成下列幾個步驟
* 選定目標
* 數據蒐集
* 斷字斷句(非結構化轉結構化)
* 資料分析
* 資料視覺化

## 安裝套件
devtools, rvest, jiebaR, jiebaRD, wordcloud2

wordcloud2 請以 devtools::install_github("lchiffon/wordcloud2") 方式安裝

## stopwords.txt
用來建立虛詞的檔案，以便檢索出關鍵字時，可以去除不必要的文字

## 輸出範例
![範例圖片](https://github.com/telunyang/R-Textual-Analysis/blob/master/Rplot.png)

## 基礎程式碼
```
library(devtools)
library(jiebaR)
library(jiebaRD)
library(wordcloud2)
library(rvest)

# 欲抓取的網址
url <- "https://web-archive-2017.ait.org.tw/zh/declaration-of-independence.html"

# 建立空字串
str_text <- as.character()

# 讀取網頁 html 元素
webpage <- read_html(url)

# 找出合適的段落
div <- html_nodes(webpage, 'div#middle-content-article')

# 將段落賦值至字串變數中
str_text <- append(str_text, html_text(div))

# 去除多餘的符號
str_text <- gsub("[\r\n\t ]", "", str_text)

# 關鍵字設定
keyword_setting <- worker(type = "mix", stop_word = "stopwords.txt")

# 整理、顯示關鍵字
#keywords(str_text, keyword_setting)

# 分割出所有關鍵字
seg <- segment(str_text, keyword_setting)

# 找出所有關鍵字的出現頻率(詞頻)
segfreq <- table(seg)

# 將所有關鍵字進行排序(從小到大)，並取出前 100 個詞
segfreq <- sort(segfreq, decreasing = TRUE)[1:500]

# 列出排序好的前 100 個詞
head(segfreq, 500)

# 建立為分詞改顏色的 function
js_color_func = "function (word, weight){
return (weight >= 10) ? '#D02090' : '#CDB5CD';
}"

# 建立詞雲
wordcloud2(
  segfreq, 
  size = 0.8,
  color = htmlwidgets::JS(js_color_func),
  shape = "triangle",
  minRotation = -pi/2,
  maxRotation = -pi/2,
  figPath = "bg1.png"
  )
```