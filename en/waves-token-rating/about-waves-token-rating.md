# About Waves Token Rating

[**Waves Token Rating **](https://tokenrating.wavesexplorer.com/)is a service that provides ratings for tokens \(projects\) issued on the Waves platform. Ratings are based on opinions of community members \(WCT token holders\) on a specific token.

## Main page

### Ratings table

The ratings table lists tokens that have already been rated.

![](../waves-token-rating/waves-token-rating-pic/image2019-4-29_13-54-54.png)

| Column | **Description** |
| :--- | :--- |
| Name | Token name |
| Rating | The current token rating is from 1 to 5, rounded to one decimal place |
| Total token votes | The number of tokens voted \(sum of weight of scores\) |
| Total amount | Total number of tokens issued |
| Price | Current token price in USD from DEX. Updated every 3 hours. |

Tokens that have not yet been rated by anyone can be found using the search bar, either by name or by token ID.

A click on the rating row opens the token page.

### Search bar

Tokens that have not yet been rated by anyone can be found using the search bar, either by name or by token ID.

If the token already has a rating, then its current rating will be indicated next to it.

When you select a token, its page opens.

![](../waves-token-rating/waves-token-rating-pic/image2019-4-29_13-52-49.png)

### Filters

If ‘All tokens’ is selected, then the table will display all tokens.

If ‘Approved’ is selected, then you will see only those tokens that have minimum rating and a certain number of total votes.  
![](../waves-token-rating/waves-token-rating-pic/image2019-4-29_13-53-29.png)

### Localization

Switch Russian and English interface languages

![](../waves-token-rating/waves-token-rating-pic/image2019-4-29_13-55-58.png)

### F.A.Q

This section contains some general information about the Waves Token Rating and a link to this description.

![](../waves-token-rating/waves-token-rating-pic/image2019-4-29_13-55-28.png)

## Token page

The token page contains detailed information about the token and its rating. The title of the card is the name of the token.  
![](../waves-token-rating/waves-token-rating-pic/image2019-4-29_14-2-35.png)

The top part of the token page displays the current token rating \(if any\) with details on the number of tokens who voted for each of the scores. Below are five stars with which the user can rate the token, the Rate button, which becomes active when the token is rated, and a hint.

Next is the **Market information **block \(if it's present\):

| Field name | Description |
| :--- | :--- |
| Price | Price from DEX |
| Cap | Multiplication of quantity and price |

Further goes the **General information **block:

![](../waves-token-rating/waves-token-rating-pic/image2019-4-29_14-3-8.png)

| Field name | Description |
| :--- | :--- |
| ID | Token ID. |
| Name | Token name. |
| Total amount | A total number of tokens issued. |
| Decimal points | A number of symbols after the comma. |
| Type | Not reissuable or reissuable |
| Issuer | Sender address. |
| Block | The block in which the token release transaction fell \(height\). |
| Issue date | Token release date. |
| Description | Token description. |

## Restrictions

Tokens can be rated from desktop browsers only; for mobile browsers, the service is available to read-only.

You can rate a token if Waves Keeper is installed in your browser, and you have at least 1 WCT in your balance.

WCT tokens are not transferred, only on existing balance is required for voting. Each vote is weighted according to the number of WCT in your balance.

## To rate a token

To rate a token, select it in the ratings table or in the search bar, then on the token page click on 1 to 5 stars. Then, click the Rate button and sign the transaction using Waves Keeper - your vote will be applied after 24 hours.

You can rate a token from each address only once. If you have already rated the selected token, you will see your previous score on the token page. In this case, you can change your score if you rate this token again. Your previous score will be canceled, and the new one will be applied.

If no one has yet rated a token, then after you vote it will listed in the ratings table, but without a value, until the rating is calculated.

## Data Transaction of user score

When the user has to rate a token, a date-transaction is formed with the following fields:

| Filed name | Data type | Required | Description |
| :--- | :--- | :--- | :--- |
| tokenRating | string | + | A service field that allows to find transactions with the user scores of tokens in the Token Rating. |
| assetId | string | + | Token ID. |
| score | integer | + | User score |

An example of the data array is the data transaction with the user score:

```js
{
    "key": "tokenRating",
    "type": "string",
    "value": "tokenRating"
}, {
    "key": "assetId",
    "type": "string",
    "value": "BrjUWjndUanm5VsJkbUip8VRYy6LWJePtxya3FNv4TQa"
}, {
    "key": "score",
    "type": "integer",
    "value": 4
}
```

## Assessment and rating calculation 

### How is the user score formed? 

The score is discrete and can take values \[1, 2, 3, 4, 5\]. The number of WCT tokens on the balance sheet has a non-linear effect on the final assessment weight. The more tokens on the balance, the lower the coefficient.

Weight is calculated by the formula:

_**W = B¹ × k  **_\(1\), where

_**W**_– the total estimated weight of the score, rounded to the full value,

_**B¹**_– effective balance,

_**k**_– coefficient, which is calculated by the formula \(rounded to hundredths\):

_**B¹∈**\[1, 10\],**k = 1**_

_**B¹ ∈ \(**10, 150 000\], **k = – 0,091 × ln\(B¹\) + 1,20958**_

_**B¹ ∈**\(150 000, 540 000\], **k = \(– 0,00019 × B¹ + 153\) ÷ 1000**_

_**B¹ ∈**\(540 000, ∞\)**, k = 0,05**_

Calculation of the effective balance:

The current balance is recorded at the time of voting _**B **WCT. Within 24 hours, all WCT **spending **operations are checked at this address and their volume is summarized. After 24 hours, the amount of WCT token transactions \(if any\) outgoing within 24 hours is subtracted from _**B**_. The result is an effective balance. _**B¹. **If its value is&gt; 0, apply formula \(1\) to calculate the total weight.

The resulting value \(score and weight\) is recorded in the blockchain as the final score from the user. If the value is &lt;0, the score is not counted.

## Rating calculation 

The current rating is an instantaneous weighted average of user scores.

### Example 
User1 rated the token at 5 stars. At the time of voting on his account 10,000 WCT.

There were 3 operations on his accounts in 24 hours: spending 300 WTC, spending 200 WTC and income 500 WTC. Therefore, total spending transactions amount to 300 + 200 = 500. 9500 - is an effective balance **B¹ **for the calculation, despite the fact that the same 10,000 WCT remained on the account after 24 hours as at the time of the assessment.

_**B¹ **is in the interval \(10, 150 000\], it means that we calculate _**k **by the formula:

_**k = – 0,091 × ln\(B¹\) + 1,20958**_=-0.091 \* ln\(9500\) + 1.20958 = 0,38

_**W = B¹ × k**_= 0,38 \* 9500 = 3610

Total: the token will receive a rating of 5 with a weight of 3610 from_User1_.

Another\_User2\_rated the same token at 4 stars. At the time of voting on his account 7 WCT.

There were no account transactions for 24 hours, so the effective balance is 7 WCT.

_B¹  \_is in between \[1, 10\], in this case _**k = 1**

_**W = B¹ × k**_= 7 \* 1 = 7

Total: the token will receive a rating of 4 with a weight of 7 from _User2._

The **token rating **will be the weighted average of the scores: Rating = \(5\*3610 + 4\*7\) / 3617 = 4.998 \(rounded up to 5.0\)

The total tokens votes will be: for 5 - 3.6k, for 4 - 7.

![](../waves-token-rating/waves-token-rating-pic/Example rating calculate.png)

Oracle Token Rating daily publishes a date in Waves blockchain with a transaction with a rating of tokens with which it has changed in the past 24 hours. In a one data-transaction is not more than 100 changes, therefore, if there are more, then several transactions are published.

Token Rating oracle card in Waves Oracles: [https://oracles.wavesexplorer.com/oracle/GgR4urzrig9p8VxHkUmnpZRfxLbzb9xFHUEABnZqF6tG](https://oracles.wavesexplorer.com/oracle/GgR4urzrig9p8VxHkUmnpZRfxLbzb9xFHUEABnZqF6tG)

Oracle Token Rating data-transaction consists of an array of “glued” keys:

| Key | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| assetRating\_assetId | string |  | Rating token with assetId. assetId should be replaced real value. \(see example below\) |

An example of the data \[ \] array is the Oracle Token Chart  data-transaction:

```js
{
    "key": "assetRating_62LyMjcr2DtiyF5yVXFhoQ2q414VPPJXjsNYp72SuDCH",
    "type": "string",
    "value": "4.5"
}, {
    "key": "assetRating_4QUMfcxQB112bZdyoAPrp1oTVN4cBA68NpGkD7W3n33i",
    "type": "string",
    "value": "3.9"
}
...
```


