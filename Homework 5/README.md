# 會回你的 ChatBot
***
 #### [說明](https://github.com/41071119H-Irene/LAT/tree/main/Homework%204#%E8%AA%AA%E6%98%8E-1)
 #### [實際互動結果](https://github.com/41071119H-Irene/LAT/tree/main/Homework%204#%E5%AF%A6%E9%9A%9B%E4%BA%92%E5%8B%95%E7%B5%90%E6%9E%9C-1)
 #### [重點code 回顧](https://github.com/41071119H-Irene/LAT/tree/main/Homework%204#%E9%87%8D%E9%BB%9E-code-%E5%9B%9E%E9%A1%A7)
***
## 說明
### 作業分成 3 個 Level
- Level 1: 直接辨識訊息內容為 positive/negetive/netural(系統原定)
- Level 2: 將系統原訂標籤改成中文(正向/負向/中性)，並顯示信心指數
- Level 3: 客製化回覆，針對訊息主詞套入正/負向句型

## 實際互動結果 
- Level 1

![hw4-1](https://user-images.githubusercontent.com/112916890/236119408-202bee25-cea9-4d4f-8e0f-1ff066cc79ad.png)
```
✨只有原本 Microsoft 的 tags : positive, negative, netrual
```
- Level 2 

![hw4-2](https://user-images.githubusercontent.com/112916890/236119692-d6246a19-98bf-4b50-9553-32bbb71c5b39.png)

```
✨tags 變成中文，後面附上信心指標
```
 - Level 2.5
 
![image](https://user-images.githubusercontent.com/112916890/236120027-d2dc201b-b280-4012-a6c4-a87518f55cbd.png)

```
✨依tags客製化回覆，但是沒辦法抓出主詞
(選取的地方不正確)
```

- Level 3

![hw4-key word](https://user-images.githubusercontent.com/112916890/236120196-bab20d52-f838-4369-af33-b08100f576f9.png)

```
✨可以針對主詞進行客製化回覆(螢光筆處即為主詞)
```
## 重點 code 回顧
### Level 1 & 2
###   1. 客製化標籤: ``` results[0].sentiment === '(原訂tag)' ```
###   2. 訊息顯示形式: ```echo``` 裡面的 ```text``` 
```
async function MS_TextSentimentAnalysis(thisEvent){
    console.log("[MS_TextSentimentAnalysis] in");
    const analyticsClient = new TextAnalyticsClient(endpoint, new AzureKeyCredential(apiKey));
    let documents = [];
    documents.push(thisEvent.message.text);
    const results = await analyticsClient.analyzeSentiment(documents);
    console.log("[results]", JSON.stringify(results));
    const score = results[0].confidenceScores;
    const scoreText = `（正面：${score.positive.toFixed(2)}、負面：${score.negative.toFixed(2)}、中立：${score.neutral.toFixed(2)}）`;
    const sentimentText = results[0].sentiment === 'positive' ? '正面情緒' : results[0].sentiment === 'negative' ? '負面情緒' : '中立情緒';
    const echo ={
        type: 'text',
        text: `${sentimentText} ${scoreText}`
    };
    return client.replyMessage(thisEvent.replyToken, echo);   
}
```

### Level 3
###   1. 客製化訊息: 設定空字串```replyText```，運用if迴圈辨識情緒
###   2. 訊息顯示形式: ```echo``` 裡面的 ```text``` 

```
async function MS_TextSentimentAnalysis(thisEvent){
    console.log("[MS_TextSentimentAnalysis] in");
    const analyticsClient = new TextAnalyticsClient(endpoint, new AzureKeyCredential(apiKey));
    let documents = [];
    documents.push(thisEvent.message.text);
    const results = await analyticsClient.analyzeSentiment(documents, "zh-Hant", { includeOpinionMining: true });
    console.log("[results]", JSON.stringify(results));
    const score = results[0].confidenceScores;
    const sentiment = results[0].sentiment;
    let replyText = '';
    if (sentiment === 'positive') {
        if (results[0].sentences[0].opinions && results[0].sentences[0].opinions.length > 0) {
            replyText = `感謝您對於本店的${results[0].sentences[0].opinions[0].target.text}感到滿意，歡迎下次再度光臨。`;
        } else {
            replyText = '感謝您的回饋，歡迎下次再度光臨。';
        }
    } else if (sentiment === 'negative') {
        if (results[0].sentences[0].opinions && results[0].sentences[0].opinions.length > 0) {
            replyText = `不好意思讓您有不好的體驗，我們將再針對${results[0].sentences[0].opinions[0].target.text}的部分進行改進。`;
        } else {
            replyText = '不好意思讓您有不好的體驗，我們會努力改進服務品質。';
        }
    } else {
        replyText = '謝謝您的回饋，我們會持續努力提升服務品質。';
    }
    
    console.log("[opinions]", results[0].sentences[0].opinions);

    const echo ={
        type: 'text',
        text: replyText
    };
    
    return client.replyMessage(thisEvent.replyToken, echo);   
}
```








