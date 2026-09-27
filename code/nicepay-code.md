# Response code

<details class="page-toc" markdown="1">
<summary>On this page</summary>

* TOC
{:toc}

</details>


<br>

## HTTP status code

| HTTP status | Description |
|:----------:|:-----------------------------------------------|
| 200 | The request reached the API. Check `resultCode`: only `0000` is success, and most errors also come with `200` |
| 401 | Authentication failed: [`U104`](#code-u104), or on the Access token API [`U101`](#code-u101), [`U116`](#code-u116), [`U117`](#code-u117) or [`U304`](#code-u304). Also [`U108`](#code-u108), unauthorized request. See [When authentication fails](../info/nicepay-info-basic-token.md#when-authentication-fails) |
| 403 | Forbidden: [`U109`](#code-u109), or [`U103`](#code-u103) on the Access token API |
| 404 | Not found: [`U107`](#code-u107) (no such transaction), [`U121`](#code-u121) (no such authentication request) or [`U316`](#code-u316) (invalid merchant settings) |
| 405 | Method Not Allowed: [`U308`](#code-u308), the endpoint does not accept this HTTP method |

NicePay does not return `400`. Treat a `5xx` status, or a response without a JSON body, as an unknown result, and look the payment up as described in [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information).


<br>

## Card code

| Code | Korean | English | Hosted Payment Page | Representative website |
|:---:|:-------------|:-------------|:---:|:---------------------------------|
| 01  | <span lang="ko">비씨</span>           | BC           |  Yes  | <https://www.bccard.com/>          |
| 02  | <span lang="ko">KB국민</span>         | KB Kookmin   |  Yes  | <https://card.kbcard.com/>         |
| 03  | <span lang="ko">하나(외환)</span>       | KEB Bank     |  Yes  | <https://www.hanacard.co.kr/>      |
| 04  | <span lang="ko">삼성</span>           | SAMSUNG      |  Yes  | <https://www.samsungcard.com/>     |
| 06  | <span lang="ko">신한</span>           | SHINHAN      |  Yes  | <https://www.shinhancard.com/>     |
| 07  | <span lang="ko">현대</span>           | HYUNDAI      |  Yes  | <https://www.hyundaicard.com/>     |
| 08  | <span lang="ko">롯데</span>           | LOTTE        |  Yes  | <https://www.lottecard.co.kr/>     |
| 11  | <span lang="ko">씨티</span>           | CITI         |  Yes  | <https://www.citibank.co.kr/>      |
| 12  | <span lang="ko">NH채움</span>         | NH           |  Yes  | <https://card.nonghyup.com/>       |
| 13  | <span lang="ko">수협</span>           | SUHYUP       |  Yes  | <https://www.suhyup-bank.com/>     |
| 14  | <span lang="ko">신협</span>           | SHINHYUP     |  Yes  | <http://www.cu.co.kr/>             |
| 15  | <span lang="ko">우리</span>           | WOORI        |  Yes  | <https://pc.wooricard.com/>        |
| 16  | <span lang="ko">하나</span>           | HANA SK      |  Yes  | <https://www.hanacard.co.kr/>      |
| 21  | <span lang="ko">광주</span>           | KWANGJU      |  Yes  | <https://pib.kjbank.com/>          |
| 22  | <span lang="ko">전북</span>           | JEONBUK      |  Yes  | <https://www.jbbank.co.kr/>        |
| 23  | <span lang="ko">제주</span>           | JEJU         |  Yes  | <https://www.jejubank.co.kr/>      |
| 24  | <span lang="ko">산은캐피탈</span>        | KDB Capital  |  Yes  | <https://www.kdbc.co.kr/cardhome>  |
| 25  | <span lang="ko">해외비자</span>         | VISA         |  Yes  | <https://www.visakorea.com/>       |
| 26  | <span lang="ko">해외마스터</span>        | MASTER       |  Yes  | <https://www.mastercard.us/>       |
| 27  | <span lang="ko">해외다이너스</span>       | DINERS       |  Yes  | <https://www.dinersclub.com/>      |
| 28  | <span lang="ko">해외AMX</span>        | AMEX         |  Yes  | <https://www.americanexpress.com/> |
| 29  | <span lang="ko">해외JCB</span>        | JCB          |  Yes  | <https://www.jcb.co.jp/>           |
| 31  | SK-OKCashBag | SK-OKCashBag | No  | <https://www.okcashbag.com>        |                                  
| 32  | <span lang="ko">우체국</span>          | Post         |  Yes  | <https://www.epostbank.go.kr/>     |
| 33  | <span lang="ko">저축은행</span>         | Savings Bank |  Yes  | <http://sbcheck.bccard.com/>       |
| 34  | <span lang="ko">은련</span>           | UnionPay     |  Yes  | <https://www.unionpayintl.com/>    |
| 35  | <span lang="ko">새마을금고</span>        | MG           |  Yes  | <https://www.kfcc.co.kr/>          |
| 36  | <span lang="ko">KDB산업</span>        | KDB Bank     |  Yes  | <https://www.kdb.co.kr/>           |
| 37  | <span lang="ko">카카오뱅크</span>        | Kakao Bank   |  Yes  | <https://www.kakaobank.com/>       |
| 38  | <span lang="ko">케이뱅크</span>         | KBank        | No  | <https://www.kbanknow.com/>        |                                  
| 39  | <span lang="ko">페이코포인트</span>       | PAYCO        |  Yes  | <https://www.payco.com/>           |
| 40  | <span lang="ko">카카오머니</span>        | KAKAO        |  Yes  | <https://www.kakaopay.com/>        |
| 41  | <span lang="ko">SSG머니</span>        | SSG          |  Yes  | <https://www.ssgpay.com/>          |
| 42  | <span lang="ko">네이버포인트</span>       | NAVER        |  Yes  | <https://www.naverfincorp.com/>    |
| 44  | <span lang="ko">토스뱅크</span>       | Toss Bank        |  Yes  | <https://www.tossbank.com/>    |
| 46  | <span lang="ko">토스머니</span>       | Toss Money       |  Yes  |                               |

<br>

## Bank code

| Code | Korean | English | Virtual Account Issuance | Representative website     |
|-----|:---------:|:-----------------------------------------------------------------|:------:|:-------------------------------------|
| 001 |   <span lang="ko">한국은행</span>    | Bank of Korea                                                    |   No   | <http://www.bok.or.kr/>                |                                      
| 002 |   <span lang="ko">산업은행</span>    | KDB Bank                                                         |   No   | <https://www.kdb.co.kr/>               |                                      
| 003 |   <span lang="ko">기업은행</span>    | IBK                                                              |   Yes    | <https://www.ibk.co.kr/>               |
| 004 |   <span lang="ko">국민은행</span>    | Kookmin Bank                                                     |   Yes    | <https://www.kbstar.com/>              |
| 005 |   <span lang="ko">외환은행</span>    | Korea Exchange Bank                                              |   Yes    | <https://www.kebhana.com/>             |
| 007 |   <span lang="ko">수협중앙회</span>   | Suhyup Federation                                                |   No   | <https://www.suhyup.co.kr/>            |
| 008 |   <span lang="ko">수출입은행</span>   | The Export-Import Bank of Korea                                  |   No   | <https://www.koreaexim.go.kr/>         |
| 011 |   <span lang="ko">농협중앙회</span>   | National Agricultural Cooperative Federation                     |   Yes    | <https://www.nonghyup.com/>            |
| 012 |  <span lang="ko">농협회원조합</span>   | National Agricultural Cooperative Federation of Members          |   No   | <https://www.nonghyup.com/>            |
| 020 |   <span lang="ko">우리은행</span>    | WooriBank                                                        |   Yes    | <https://www.wooribank.com/>           |
| 023 |   <span lang="ko">SC은행</span>    | SCBank                                                           |   Yes    | <https://www.standardchartered.co.kr/> |
| 026 |   <span lang="ko">서울은행</span>    | Seoul Bank (merged into Hana Bank)                               |   No   | <https://www.kebhana.com/>             |
| 027 |  <span lang="ko">한국씨티은행</span>   | Citibank Korea                                                   |   No   | <https://www.citibank.co.kr/>          |                                      
| 031 |   <span lang="ko">대구은행</span>    | Daegu Bank                                                       |   Yes    | <https://www.dgb.co.kr/>               |
| 032 |   <span lang="ko">부산은행</span>    | Busan Bank                                                       |   Yes    | <https://www.busanbank.co.kr/>         |
| 034 |   <span lang="ko">광주은행</span>    | KwangjuBank                                                      |   Yes    | <https://pib.kjbank.com/>              |
| 035 |   <span lang="ko">제주은행</span>    | Jeju Bank                                                        |   No   | <https://www.jejubank.co.kr/>          | 
| 037 |   <span lang="ko">전북은행</span>    | JeonBuk Bank                                                     |   No   | <https://www.jbbank.co.kr/>            |                                      
| 039 |   <span lang="ko">경남은행</span>    | Kyongnam Bank                                                    |   No   | <https://www.knbank.co.kr/>            |                                      
| 045 | <span lang="ko">새마을금고연합회</span>  | KFCC Federation                                                  |   No   | <https://www.kfcc.co.kr/>              |                                      
| 048 |   <span lang="ko">신협중앙회</span>   | National Credit Union Federation of Korea                        |   No   | <http://www.cu.co.kr/>                 |                                      
| 050 |  <span lang="ko">상호저축은행</span>   | Mutual Savings Bank                                              |   No   | <https://www.fsb.or.kr/>               |                                      
| 071 | <span lang="ko">정보통신부 우체국</span> | Postal Savings for the Ministry of Information and Communication |   Yes    | <https://www.epostbank.go.kr/>         |
| 081 |   <span lang="ko">하나은행</span>    | HanaBank                                                         |   Yes    | <https://www.kebhana.com/>             |
| 088 |   <span lang="ko">신한은행</span>    | ShinhanBank                                                      |   Yes    | <https://www.shinhan.com/>             |
| 089 |   <span lang="ko">케이뱅크</span>    | K Bank                                                           |   Yes    | <https://www.kbanknow.com/>            |
| 090 |   <span lang="ko">카카오뱅크</span>   | Kakao Bank                                                       |   No   | <https://www.kakaobank.com/>           | 
| 092 | <span lang="ko">토스뱅크</span>      | Toss Bank                                                        |  Yes      | <https://www.tossbank.com/>          |

<br>

## API response code

`U`-prefixed codes come from NicePay's API. Every other code (bare numbers, and `A`/`F`/`C`/`I`/`E`-prefixed codes) normally comes from NicePay's payment network. A few codes, including `0000`, `9999` and `F101`, can come from either, so the same code can carry a different message; the message shown below is the API's.

A success reaches your server only as `0000`. The API converts the payment network's success codes in this table, such as `3001`, `4000`, `4100`, `A000`, `2001` and `2211`, into `0000`, so treat every other code as not successful.

A handful of codes are reused with unrelated meanings depending on which API returned them (for example `2011`, `2012`, `C002`); where that happens, the domain is called out in parentheses in the English message below, e.g. "(cancel)" vs "(general/DB error)". Match that to the API you called.

Some messages carry Korean payment-industry terms straight into the English column. What they mean:

- **Net cancel** (`망취소`, written as "network cancellation" in the table below): a cancellation that reverses a card authorization when the result of that authorization was not delivered. NicePay does not cancel a payment because your server timed out. NicePay sends a net cancel on its own only when its own processing fails during an authorization, and then returns `U504` (Checkout, Key-in) or `U503` (Recurring). Your server can request one with [Net cancel](../api/nicepay-api-cancel.md#net-cancel). See [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information) for what to do after a timeout. Codes `2020`, `P035`, `U503`, `U504`.
- **CPID**: the identifier of the partner financial institution behind a bank transfer, virtual account, or mobile carrier payment. These codes mean your merchant account is not set up for that institution, not that your request was malformed. Codes `4126`, `4127`, `A303`, `M001`, `M002`.
- **PKCS7**: the signed and encrypted message format used when a token is issued. Codes `F101`, `F103`, `F111`.
- **OCSP**: the certificate revocation check run as part of that verification. Code `F105`.
- **KFTC** (`금결원`, `금융결제원`): the Korea Financial Telecommunications and Clearings Institute, which runs the interbank network behind bank transfers. Codes `4001`, `V602`.
- **VAN**: a value-added network company that relays card transactions between NicePay and the card companies. Code `7010`.
- **ARS**: authentication by an automated phone call. Codes `A150`, `A256`.
- **ISP**: the card authentication app that BC Card and KB Kookmin Card use. Code `C001`.

> **⚠️ Important:** If you receive a `resultCode` that isn't in this table, it's likely a payment network code not yet catalogued here. [Open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) with the `tid`/`orderId` and the exact `resultCode` for identification.  

<br>

|  Code  | message (korean)     | message (english)     |
|:------:|:---------------------|:----------------------|
| <span id="code-0000">0000</span> | <span lang="ko">정상 처리되었습니다.</span> | Payment or transaction successful |
| <span id="code-9999">9999</span> | <span lang="ko">일시적인 오류가 발생하였습니다.</span> | A temporary error occurred |
| <span id="code-3001">3001</span> | <span lang="ko">카드 결제 성공</span>             | Card payment successful |
| <span id="code-3011">3011</span> | <span lang="ko">카드번호 오류</span>              | Card number error |
| <span id="code-3012">3012</span> | <span lang="ko">카드가맹점 정보 미확인</span>     | Unconfirmed card merchant information |
| <span id="code-3013">3013</span> | <span lang="ko">카드 가맹점 개시 안됨</span>      | Card merchant not in service |
| <span id="code-3014">3014</span> | <span lang="ko">카드가맹점 정보 오류</span>       | Card merchant information error |
| <span id="code-3021">3021</span> | <span lang="ko">유효기간 오류</span>              | Expiration date error |
| <span id="code-3022">3022</span> | <span lang="ko">할부개월오류</span>               | Installment month error |
| <span id="code-3023">3023</span> | <span lang="ko">할부개월 한도 초과</span>         | Installment month limit exceeded |
| <span id="code-3024">3024</span> | <span lang="ko">할부 최소금액 오류(50000미만)</span>    | Minimum installment amount error (less than 50,000).      |
| <span id="code-3031">3031</span> | <span lang="ko">무이자할부 카드 아님</span>       | No interest-free installment card |
| <span id="code-3032">3032</span> | <span lang="ko">무이자할부 불가 개월수</span>         |Non-allowed interest-free installment months |
| <span id="code-3033">3033</span> | <span lang="ko">무이자할부 가맹점 아님</span>         |Not affiliated with interest-free installments|
| <span id="code-3034">3034</span> | <span lang="ko">무이자할부 구분 미설정</span>         |Interest-free installment value not set |
| <span id="code-3041">3041</span> | <span lang="ko">금액 오류(1000원 미만 신용카드 승인 불가)</span> | Amount error (Credit card under KRW 1,000 cannot be accepted) |
| <span id="code-3051">3051</span> | <span lang="ko">해외카드 미등록 가맹점</span>     | Non-registered overseas card merchants |
| <span id="code-3052">3052</span> | <span lang="ko">통화코드 오류</span>      | Currency code error |
| <span id="code-3053">3053</span> | <span lang="ko">확인 불가 해외카드</span>     | Unavailable Overseas Cards  |
| <span id="code-3054">3054</span> | <span lang="ko">환률전환오류</span>       | Exchange Rate Conversion Error |
| <span id="code-3055">3055</span> | <span lang="ko">인증시 달러승인 불가</span>       | Dollars cannot be accepted for authentication |
| <span id="code-3056">3056</span> | <span lang="ko">국내카드 달러승인불가</span>      | Dollar Authorization for Domestic card is not accepted |
| <span id="code-3057">3057</span> | <span lang="ko">인증 불가카드</span>      | Unauthorized Card |
| <span id="code-3061">3061</span> | <span lang="ko">국민카드 인터넷안전결제 적용 가맹점</span>        | Kookmin Card internet safe payment affiliates |
| <span id="code-3062">3062</span> | <span lang="ko">신용카드 승인번호 오류</span>     | Credit card approval number error |
| <span id="code-3071">3071</span> | <span lang="ko">매입요청 가맹점 아님</span>       | Capture is not allowed |
| <span id="code-3072">3072</span> | <span lang="ko">매입요청 TID 정보 불일치</span>   |  Transaction ID information inconsistency |
| <span id="code-3073">3073</span> | <span lang="ko">기매입 거래</span>    | Transaction is captured already |
| <span id="code-3081">3081</span> | <span lang="ko">카드 잔액 값 오류</span>      | Card balance error |
| <span id="code-3091">3091</span> | <span lang="ko">제휴카드 사용불가 가맹점</span>   | This merchant does not accept partnership cards |
| <span id="code-3095">3095</span> | <span lang="ko">카드사 실패 응답</span>   | Card issuers respond to failure |
| <span id="code-4000">4000</span> | <span lang="ko">계좌이체 결제 성공</span>     | Bank Transfer succeed |   
| <span id="code-4001">4001</span> | <span lang="ko">금결원오류응답</span> | Error response from KFTC |
| <span id="code-4002">4002</span> | <span lang="ko">회원사 서비스 불가 은행</span>  | Not supported bank |
| <span id="code-4003">4003</span> | <span lang="ko">출금일자 불일치</span> | Unmatched transfer date |
| <span id="code-4004">4004</span> | <span lang="ko">출금요청금액 불일치</span> | Unmatched transfer amount |
| <span id="code-4005">4005</span> | <span lang="ko">거래번호(TID) 불일치</span> | Unmatched TID |
| <span id="code-4006">4006</span> | <span lang="ko">회신 정보 불일치</span> | Unmatched responded data |
| <span id="code-4007">4007</span> | <span lang="ko">계좌이체 승인번호 오류</span> | Bank Transfer authenticated number error |
| <span id="code-4008">4008</span> | <span lang="ko">은행 시스템 서비스 중단</span> | Banking system service stopped |
| <span id="code-4100">4100</span> | <span lang="ko">가상계좌 발급 성공</span> | Virtual account generating succeed |
| <span id="code-4110">4110</span> | <span lang="ko">가상계좌 입금 성공</span> | Deposit to virtual account completed |
| <span id="code-4120">4120</span> | <span lang="ko">가상계좌 과오납체크 등록 성공</span>  | Virtual account deposit amount registration success |
| <span id="code-4101">4101</span> | <span lang="ko">가상계좌 최대거래금액 초과</span> | Maximum virtual account deposit amount exceeded |
| <span id="code-4102">4102</span> | <span lang="ko">가상계좌 입금예정일 오류</span>   | Virtual account deposit due date error |
| <span id="code-4103">4103</span> | <span lang="ko">가상계좌 입금예정시간 오류</span> | Virtual account deposit time error |
| <span id="code-4104">4104</span> | <span lang="ko">가상계좌 정보 오류</span> | Virtual account information error |
| <span id="code-4105">4105</span> | <span lang="ko">가상계좌 벌크계좌 사용불가.(건별계좌 사용 가맹점)</span>  | Bulk virtual accounts are not available|
| <span id="code-4106">4106</span> | <span lang="ko">가상계좌 벌크계좌 사용불가.(벌크계좌 사용 가맹점)</span>  | Bulk virtual accounts are not available |
| <span id="code-4107">4107</span> | <span lang="ko">가상계좌 계좌번호 미입력 오류</span>  | Virtual account number is not entered |
| <span id="code-4108">4108</span> | <span lang="ko">가상계좌 Pool 가맹점 미등록 오류</span>   | Unregistered virtual account pool service for this merchant |
| <span id="code-4109">4109</span> | <span lang="ko">해당 계좌는 입금대기상태(다른 계좌 사용요망)</span>   | The account is in a deposit waiting state (use another account) |
| <span id="code-4111">4111</span> | <span lang="ko">가상계좌 Pool 가맹점 계좌형태 설정 오류</span>    | Virtual Account Account Type Setting Error |
| <span id="code-4112">4112</span> | <span lang="ko">벌크계좌의 경우 장바구니 개수를 10개 이내로 제한</span>   | For bulk virtual account, the number of shopping carts is limited to 10 |
| <span id="code-4113">4113</span> | <span lang="ko">장바구니 상점주문번호(MOID) 포맷 오류 (MID(10)+YYMMDD(6)+일련번호(4))</span>    | Shopping Cart Order Number (MOID) Format Error (MID(10)+YYMMDD(6)+Serial Number(4)) |
| <span id="code-4114">4114</span> | <span lang="ko">장바구니 상점주문번호(MOID) 포맷 오류 (YYMMDD(6)+일련번호(max_size 14))</span>  | Shopping Cart Order Number (MOID) Format Error (YYMMDD(6)+Serial Number(max_size 14)) |
| <span id="code-4115">4115</span> | <span lang="ko">해당 계좌번호는 6개월 이내 재사용 금지</span> | This account number cannot be reused within 6 months |
| <span id="code-4116">4116</span> | <span lang="ko">가상계좌 계좌번호 채번시도 횟수초과 오류(잠시후 재시도 요망)</span>   | Virtual account request count exceeded (retry later) |
| <span id="code-4117">4117</span> | <span lang="ko">가상계좌 발급 실패</span> | Failure to issue virtual account |
| <span id="code-4118">4118</span> | <span lang="ko">계좌번호 사용중지 상태</span> | Virtual Account Number is suspended |
| <span id="code-4119">4119</span> | <span lang="ko">가상계좌 채번내역 미존재 오류</span>  | Not issued virtual account |
| <span id="code-4121">4121</span> | <span lang="ko">가상계좌 과오납체크 등록 실패</span>  | Virtual account deposit amount registration is failed |
| <span id="code-4122">4122</span> | <span lang="ko">가상계좌 입금요청 금액 불일치 오류</span> | Virtual Account Deposit Request Amount Mismatch Error |
| <span id="code-4123">4123</span> | <span lang="ko">가상계좌 채번취소(환불) 상태이므로 입금처리 실패</span>   | Deposit failed because refund requested |
| <span id="code-4124">4124</span> | <span lang="ko">가상계좌 입금내역 기처리 완료</span>  | Virtual account deposit has already been processed |
| <span id="code-4125">4125</span> | <span lang="ko">가상계좌 입금만료 제한 설정오류(기준정보)</span>  | This is a merchant that does not reflect the virtual account deposit expiration date setting option |
| <span id="code-4126">4126</span> | <span lang="ko">가상계좌 CPID 설정 오류</span>    | Virtual account CPID setting error |
| <span id="code-4127">4127</span> | <span lang="ko">가상계좌 CPID 미설정 오류</span>  | Virtual account CPID is not set |
| <span id="code-4141">4141</span> | <span lang="ko">금액오류(1원 이하 이체불가)</span>    | Amount error (Greater than 1KRW is required) |
| <span id="code-4142">4142</span> | <span lang="ko">가상계좌 과오납체크 미사용 가맹점</span>  | Virtual account deposit amount registration service is not available |
| <span id="code-4143">4143</span> | <span lang="ko">가상계좌 과오납체크 미등록 오류</span>    | Virtual account deposit amount is not set |
| <span id="code-4145">4145</span> | <span lang="ko">예금주명 수정이 불가능한 MID</span> | Account holder name is not changeable |
| <span id="code-7001">7001</span> | <span lang="ko">현금영수증 처리 성공</span>   | Issuance of cash receipt successful |
| <span id="code-7002">7002</span> | <span lang="ko">현금영수증 종류오류</span>    | cash receipt type error |
| <span id="code-7003">7003</span> | <span lang="ko">현금영수증 중복발급</span>    | Cash receipts are issued already |
| <span id="code-7004">7004</span> | <span lang="ko">현금영수증 취소오류</span>    | Cash receipt cancellation error |
| <span id="code-7005">7005</span> | <span lang="ko">현금영수증 부가가치세 오류</span> | Cash Receipt VAT setting Error |
| <span id="code-7006">7006</span> | <span lang="ko">현금영수증 최대개수 초과</span>   | Exceeds the maximum number of cash receipts |
| <span id="code-7007">7007</span> | <span lang="ko">현금영수증 요청개수 입력 오류</span>  | Requested number of cash receipts error |
| <span id="code-7008">7008</span> | <span lang="ko">현금영수증 서브몰 발행 미등록 업체</span> | Issuing cash receipt to submall is not available |
| <span id="code-7009">7009</span> | <span lang="ko">현금영수증 처리 지불수단이 아닙니다</span> | Payment method setting error |
| <span id="code-7010">7010</span> | <span lang="ko">VAN응답실패</span> | VAN response failed |
| <span id="code-7011">7011</span> | <span lang="ko">현금영수증 미발행 요청입니다</span>  | Cash receipt issuance request value error |
| <span id="code-7012">7012</span> | <span lang="ko">현금영수증 Identity 번호 오류</span>  | Cash Receipt ID number error |
| <span id="code-7013">7013</span> | <span lang="ko">현금영수증 요청구분 값 오류(1:소득공제, 2:지출증빙)</span>  | Cash Receipt request type error |
| <span id="code-7014">7014</span> | <span lang="ko">현금영수증 서브몰 사업자번호로 발행시 필수값 누락</span>  | Missing required value when issuing cash receipt for sub-mall |
| <span id="code-7015">7015</span> | <span lang="ko">현금영수증 취소시 원승인번호 또는 원거래일자 누락</span>  | Missing original approval number or original transaction date |
| <span id="code-7041">7041</span> | <span lang="ko">금액오류(0원 이하 발행불가)</span>    | Amount error (cannot be issued for KRW 0 or less) |
| <span id="code-7042">7042</span> | <span lang="ko">현금영수증 최대금액 초과오류</span>   | Cash Receipt maximum amount exceeded |
| <span id="code-a000">A000</span> | <span lang="ko">휴대폰결제 처리 성공</span>   | Phone bill payment processed successfully |
| <span id="code-a001">A001</span> | <span lang="ko">휴대폰결제 처리 실패</span>   | Phone bill payment processing failed |
| <span id="code-a002">A002</span> | <span lang="ko">필수입력값(거래키) 누락</span>    | Transcation key is missing |
| <span id="code-a003">A003</span> | <span lang="ko">필수입력값(이통사구분) 누락</span>    | Mobile carrier value is missing |
| <span id="code-a004">A004</span> | <span lang="ko">필수입력값(SMS승인번호) 누락</span>   | SMS Authorization number is missing |
| <span id="code-a005">A005</span> | <span lang="ko">필수입력값(업체TID) 누락</span>   | TID value is missing |
| <span id="code-a006">A006</span> | <span lang="ko">필수입력값(휴대폰번호) 누락</span>    | Phone number value is missing |
| <span id="code-a041">A041</span> | <span lang="ko">결제금액 오류</span>  | Amount error |
| <span id="code-a564">A564</span> | <span lang="ko">휴대폰결제ID설정 오류</span>  |  Phone bill payment ID setting error |
| <span id="code-a565">A565</span> | <span lang="ko">휴대폰결제ID미설정 오류</span>    | Phone bill payment ID is not set |
| <span id="code-a566">A566</span> | <span lang="ko">휴대폰결제사 설정 오류</span> | Mobile carrier type setting error |
| <span id="code-a567">A567</span> | <span lang="ko">상품구분코드 설정 오류</span> | Product type code setting error |
| <span id="code-a568">A568</span> | <span lang="ko">서비스구분코드 설정 오류</span>   | Service code setting error |
| <span id="code-1534">1534</span> | <span lang="ko">부분취소 불가능 거래</span>      | Partial cancellation is not available for this transaction.   |
| <span id="code-1615">1615</span> | <span lang="ko">거래금액 합계오류(공급가액,부가세,봉사료,면세금액 합계)</span>     | Total transaction amount error (sum of supply, VAT, service charge, and tax-exempt amount).  |
| <span id="code-2000">2000</span> | <span lang="ko">DB오류</span> | DB Error | 
| <span id="code-2011">2011</span> | <span lang="ko">CINO미존재</span> | CINO does not exist (general/DB error) |
| <span id="code-2012">2012</span> | <span lang="ko">주문번호없음</span>   | No order number (general/DB error) |
| <span id="code-2151">2151</span> | <span lang="ko">거래정지 가맹점</span> | Transaction suspended merchant |
| <span id="code-2152">2152</span> | <span lang="ko">미등록가맹점</span>  | Unregistered Merchants |
| <span id="code-2154">2154</span> | <span lang="ko">제휴사상태미확인</span>   | Payment partner status is not founded |
| <span id="code-2156">2156</span> | <span lang="ko">중복등록된거래요청</span> | Duplicated transaction request |
| <span id="code-2157">2157</span> | <span lang="ko">허용되지않는입력방식</span> | Input method is not allowed |
| <span id="code-2158">2158</span> | <span lang="ko">중복등록된입력방식</span> | Duplicated input method |
| <span id="code-2159">2159</span> | <span lang="ko">해당은행장애</span>   | Requested bank error found |
| <span id="code-2201">2201</span> | <span lang="ko">기승인존재</span> | Pre-approved transaction existence |
| <span id="code-f100">F100</span> | <span lang="ko">빌키가 정상적으로 생성되었습니다.</span>  | Billkey has been created successfully |
| <span id="code-f101">F101</span> | <span lang="ko">PKCS7 전자서명 및 암호화메시지 검증 실패.</span> | PKCS7 signature/encrypted-message verification failed (billing, key-in) |
| <span id="code-f102">F102</span> | <span lang="ko">인증서 검증 오류</span>  | Certificate validation error |
| <span id="code-f103">F103</span> | <span lang="ko">PKCS7 전자서명 검증 실패</span>  | PKCS7 digital signature verification failed |
| <span id="code-f104">F104</span> | <span lang="ko">인증정보 확인중 오류가 발생하였습니다</span> | An error occurred while verifying authentication information |
| <span id="code-f105">F105</span> | <span lang="ko">OCSP인증 중 오류가 발생하였습니다</span> | An error occurred during OCSP authentication |
| <span id="code-f106">F106</span> | <span lang="ko">DB 처리중 오류가 발생하였습니다</span> | A NicePay-side error occurred while processing the request |
| <span id="code-f107">F107</span> | <span lang="ko">DB 처리중 오류가 발생하였습니다</span> | A NicePay-side error occurred while processing the request |
| <span id="code-f108">F108</span> | <span lang="ko">DB 처리중 오류가 발생하였습니다</span> | A NicePay-side error occurred while processing the request |
| <span id="code-f109">F109</span> | <span lang="ko">DB 처리중 오류가 발생하였습니다</span> | A NicePay-side error occurred while processing the request |
| <span id="code-f110">F110</span> | <span lang="ko">빌키발급 처리중 오류가 발생하였습니다</span>   | An error occurred while processing billkey issuance |
| <span id="code-f111">F111</span> | <span lang="ko">PKCS7 전자서명 메시지가 존재하지 않습니다</span>   | The PKCS7 digitally signed message does not exist |
| <span id="code-f112">F112</span> | <span lang="ko">유효하지않은 카드번호를 입력하셨습니다 (card_bin 없음)</span>  | Invalid card number (no card_bin) |
| <span id="code-f113">F113</span> | <span lang="ko">본인의 신용카드 확인중 오류가 발생하였습니다</span>    | An error occurred while verifying your credit card |
| <span id="code-f114">F114</span> | <span lang="ko">인증서가 유효하지 않습니다</span>    | Certificate is not valid |
| <span id="code-f115">F115</span> | <span lang="ko">빌키 발급 불가 가맹점입니다.(중지)</span> | Can not issue billkey (Suspended) |
| <span id="code-f116">F116</span> | <span lang="ko">빌키 발급 불가 가맹점입니다.(해지)</span> | Can not issue billkey (Contract is terminated) |
| <span id="code-f117">F117</span> | <span lang="ko">빌링 미사용 가맹점입니다</span>  | Billing service agreement required |
| <span id="code-f118">F118</span> | <span lang="ko">해당카드는 사용이 불가능 합니다 타사카드를 이용해주세요</span>  | The card cannot be used Please use a another card |
| <span id="code-f200">F200</span> | <span lang="ko">빌링 요청승인이 정상적으로 이루어졌습니다</span> | The billing request has been approved successfully |
| <span id="code-f201">F201</span> | <span lang="ko">이미 등록된 카드 입니다(빌키발급실패)</span>   | This card has been registered already (failed to issue bill key) |
| <span id="code-c000">C000</span> | <span lang="ko">에스크로배송등록 성공</span>  | Escrow delivery registration successful |
| <span id="code-c002">C002</span> | <span lang="ko">에스크로 가맹점 아님</span>   | Not an Escrow Merchant (escrow delivery registration) |
| <span id="code-c003">C003</span> | <span lang="ko">에스크로 거래만 배송등록 가능</span>  | Shipping register can be possible only for escrow transactions |
| <span id="code-c004">C004</span> | <span lang="ko">에스크로결제 신청내역 미존재</span>   | Escrow payment history does not exist |
| <span id="code-c005">C005</span> | <span lang="ko">에스크로배송등록 불가상태</span>  | Unable to Register Escrow Shipping |
| <span id="code-c006">C006</span> | <span lang="ko">거래내역이 존재하지 않음</span>  | Transaction history does not exist |
| <span id="code-c007">C007</span> | <span lang="ko">취소된 거래는 배송등록 불가</span>   | Canceled transactions, Can not be registered for delivery |
| <span id="code-d000">D000</span> | <span lang="ko">에스크로구매결정 성공</span>  |  Escrow purchase decision successful |
| <span id="code-d002">D002</span> | <span lang="ko">에스크로 가맹점 아님</span>   | Not an Escrow Merchant |
| <span id="code-d003">D003</span> | <span lang="ko">에스크로 거래만 구매결정 가능</span>  | Only escrow transactions can make purchase decisions request |
| <span id="code-d004">D004</span> | <span lang="ko">에스크로결제 신청내역 미존재</span>   | Escrow payment history does not exist | 
| <span id="code-d005">D005</span> | <span lang="ko">에스크로구매결정 불가상태</span>  | Unable to update purchase decisions request for Escrow|
| <span id="code-d006">D006</span> | <span lang="ko">거래내역이 존재하지 않음</span>  | Transaction history does not exist  |
| <span id="code-d007">D007</span> | <span lang="ko">고객고유번호 미입력</span>   | Customer identification number is not entered |
| <span id="code-d008">D008</span> | <span lang="ko">거래요청내역이 존재하지 않음</span>  | Transaction request history do not exist  |
| <span id="code-d009">D009</span> | <span lang="ko">고객고유번호 검증 실패</span>   | Customer identification number verification failed |
| <span id="code-d010">D010</span> | <span lang="ko">구매결정 이미 처리됨</span>  | Purchase decision already processed |
| <span id="code-d011">D011</span> | <span lang="ko">취소된 거래는 구매결정 불가</span>   | Canceled transactions can not be make purchase decisions request |
| <span id="code-e000">E000</span> | <span lang="ko">에스크로구매거절 성공</span>  | Escrow Purchase Refusal Success |
| <span id="code-e002">E002</span> | <span lang="ko">에스크로 가맹점 아님</span>   |  Not an Escrow Merchant |
| <span id="code-e003">E003</span> | <span lang="ko">에스크로 거래만 구매거절 가능</span>  | Only Escrow transactions can make Escrow Purchase Refusal |
| <span id="code-e004">E004</span> | <span lang="ko">에스크로결제 신청내역 미존재</span>   | Escrow request history does not exist |
| <span id="code-e005">E005</span> | <span lang="ko">에스크로구매거절 불가상태</span>  | Unable to refuse escrow purchase |
| <span id="code-e006">E006</span> | <span lang="ko">거래내역이 존재하지 않음</span>  |  Transaction history does not exist |
| <span id="code-e007">E007</span> | <span lang="ko">고객고유번호 미입력</span>   | Customer identification number is not entered |
| <span id="code-e008">E008</span> | <span lang="ko">거래요청내역이 존재하지 않음</span>  | Transaction request is not found |
| <span id="code-e009">E009</span> | <span lang="ko">고객고유번호 검증 실패</span>    | Customer identification number verification failed |
| <span id="code-e010">E010</span> | <span lang="ko">구매거절 이미 처리됨</span>  | Escrow Purchase Refusal has been processed already |
| <span id="code-e011">E011</span> | <span lang="ko">취소된 거래는 구매거절 불가</span>  | Canceled transactions can not make Escrow Purchase Refusal |
| <span id="code-2001">2001</span> | <span lang="ko">취소 성공</span> | Cancellation successful |
| <span id="code-2211">2211</span> | <span lang="ko">환불 성공 (2001과 함께 취소 성공 처리할 것)</span> | Refund successful. Handle it as a successful cancellation, like `2001` |
| <span id="code-2003">2003</span> | <span lang="ko">취소 실패</span>  | Cancellation Failed |
| <span id="code-2010">2010</span> | <span lang="ko">취소 요청금액 0원 이하</span> | Cancellation amount is KRW 0 or less |
| 2011 | <span lang="ko">취소 금액 불일치</span>   | Cancellation amount discrepancy (cancel) |
| 2012 | <span lang="ko">취소 해당거래 없음</span> | No applicable transaction (cancel) |
| <span id="code-2013">2013</span> | <span lang="ko">취소 완료 거래</span> | Canceled Transactions |
| <span id="code-2014">2014</span> | <span lang="ko">취소 불가능 거래</span>   | Non-cancellable transaction |
| <span id="code-2015">2015</span> | <span lang="ko">해당거래 취소실패(기취소성공)</span> | Cancellation failed because the transaction was already cancelled |
| <span id="code-2016">2016</span> | <span lang="ko">취소 기한 초과</span> | Cancellation Deadline Exceeded |
| <span id="code-2017">2017</span> | <span lang="ko">취소 불가 회원사</span>   |  Non-cancellable partner |
| <span id="code-2018">2018</span> | <span lang="ko">신용카드 매입후 취소 불가능 가맹점</span> | Non-cancellable partner after card capture |
| <span id="code-2019">2019</span> | <span lang="ko">타 회원사 거래 취소 불가</span>   | Non-Cancellable Merchants |
| <span id="code-2020">2020</span> | <span lang="ko">망상 취소 허용시간 초과</span>   | Exceeded the allowed time window for a net cancel  |
| <span id="code-2021">2021</span> | <span lang="ko">매입전취소</span> | Cancellation before card capture |
| <span id="code-2022">2022</span> | <span lang="ko">매입후취소</span> | Cancellation after card capture |
| <span id="code-2023">2023</span> | <span lang="ko">취소 한도 초과</span> | Cancellation Limit Exceeded |
| <span id="code-2024">2024</span> | <span lang="ko">취소패스워드 불일치</span>    | Cancel password mismatch |
| <span id="code-2025">2025</span> | <span lang="ko">취소패스워드 미 입력</span>   | Cancel password is not entered |
| <span id="code-2026">2026</span> | <span lang="ko">입금액보다 취소금액이 큽니다.</span>  | The cancellation amount is larger than the deposit amount |
| <span id="code-2027">2027</span> | <span lang="ko">에스크로 거래는 구매 또는 구매거절 시 취소가능</span> | Escrow transactions are cancelable after purchase or rejection request |
| <span id="code-2028">2028</span> | <span lang="ko">부분취소 불가능 가맹점</span> | Partial cancellation is not allowed for this merchant |
| <span id="code-2029">2029</span> | <span lang="ko">부분취소 불가능 결제수단</span>   | Partial cancellation is not allowed for this payment method |
| <span id="code-2030">2030</span> | <span lang="ko">해당결제수단 부분취소 불가</span>  | Partial cancellation is not allowed for this payment method |
| <span id="code-2031">2031</span> | <span lang="ko">전체금액취소 불가</span>  | You can not cancel the total amount |
| <span id="code-2032">2032</span> | <span lang="ko">취소금액이 취소가능금액보다 큼</span>  | Requested Cancellation amount is greater than cancelable amount (cancel) |
| <span id="code-2033">2033</span> | <span lang="ko">부분취소 불가능금액 전체취소 이용바람</span> | Partial cancellation is not possible, total cancellation is required |
| <span id="code-2045">2045</span> | <span lang="ko">채번취소 처리시 부분취소 불가능</span> | Partial cancellation is not allowed when cancelling a bulk virtual-account issuance |
| <span id="code-2052">2052</span> | <span lang="ko">에스크로 부분취소 불가.</span>   | Escrow partial cancellation not allowed.     |
| <span id="code-a101">A101</span> | <span lang="ko">SIGN DATA 검증에 실패하였습니다</span>   | SIGN DATA verification failed |
| <span id="code-a102">A102</span> | <span lang="ko">"타입이 맞지않는 파라미터명 명시" + 은(는) 알 수 없는 TYPE 입니다</span> | Does not match the type |
| <span id="code-a106">A106</span> | <span lang="ko">날짜 형식이 올바르지 않습니다</span> | The date format is incorrect |
| <span id="code-a107">A107</span> | <span lang="ko">입금예정일이 지났습니다</span>   | The deposit due date has passed |
| <span id="code-a108">A108</span> | <span lang="ko">입금예정일을 오늘 이전 날짜로 설정할 수 없습니다</span>  |You cannot set the deposit due date before today |
| <span id="code-a109">A109</span> | <span lang="ko">요청한 MID의 설정 정보가 없습니다</span> | The configuration information for the requested MID does not exist |
| <span id="code-a110">A110</span> | <span lang="ko">외부 연동결과 실패에 대한 코드(외부 에러메세지 그대로 전달)</span>    | External integration result failure code |
| <span id="code-a111">A111</span> | <span lang="ko">다날 휴대폰 통신 오류입니다</span>   | Danal mobile communication error |
| <span id="code-a112">A112</span> | <span lang="ko">비정상적인 경로로 접속되었습니다</span>  | Access was made through an abnormal path |
| <span id="code-a113">A113</span> | <span lang="ko">결제 금액이 최소 금액보다 적습니다</span>    | The payment amount is less than the minimum amount |
| <span id="code-a114">A114</span> | <span lang="ko">상점 MID가 유효하지 않습니다</span>  | The store MID is invalid |
| <span id="code-a115">A115</span> | <span lang="ko">TID가 유효하지 않습니다</span>   | The TID is invalid |
| <span id="code-a116">A116</span> | <span lang="ko">요청 금액이 올바르지 않습니다</span> | The requested amount is invalid |
| <span id="code-a117">A117</span> | <span lang="ko">필수입력항목이 누락되었습니다</span> | Required input items are missing |
| <span id="code-a118">A118</span> | <span lang="ko">조회 결과데이터 없음</span>   | No query result data | 
| <span id="code-a119">A119</span> | <span lang="ko">일반 무이자 이벤트 조회 결과 없음</span>  | No query result for general interest-free event |
| <span id="code-a120">A120</span> | <span lang="ko">부분 무이자 이벤트 조회 결과 없음</span>  | No query result for partial interest-free event |
| <span id="code-a121">A121</span> | <span lang="ko">정의되지 않은 카드코드 입니다</span> |  Undefined card code |
| <span id="code-a122">A122</span> | <span lang="ko">타 상점 거래 처리 불가(MID 불일치)</span> | Unable to process transaction for another store (MID mismatch) |
| <span id="code-a123">A123</span> | <span lang="ko">거래금액 불일치(인증된 금액과 승인요청 금액 불일치)</span>    | Transaction amount mismatch (authenticated amount and approved request amount do not match)  |
| <span id="code-a124">A124</span> | <span lang="ko">해당 BID가 존재하지 않습니다</span>  | The corresponding BID does not exist |
| <span id="code-a125">A125</span> | <span lang="ko">BID가 유효하지 않습니다</span>   | The BID is invalid |
| <span id="code-a126">A126</span> | <span lang="ko">이미 삭제된 빌키이거나 존재하지 않은 빌키입니다</span>    | The bill key has already been deleted or does not exist |
| <span id="code-a127">A127</span> | <span lang="ko">주문번호 중복 오류</span> |  Order number duplication error |
| <span id="code-a128">A128</span> | <span lang="ko">키인 가맹점 아닙니다</span>  |  Not a key-in merchant | 
| <span id="code-a144">A144</span> | <span lang="ko">가맹점 결과 통보에 실패하였습니다</span> | Failed to notify merchant result |
| <span id="code-a145">A145</span> | <span lang="ko">외화 결제는 결제수단 지정하여 이용 가능 합니다</span>  | Foreign currency payment is available by specifying a payment method |
| <span id="code-a146">A146</span> | <span lang="ko">외화 결제 불가한 결제수단 입니다</span>  | Payment method not available for foreign currency payment |
| <span id="code-a147">A147</span> | <span lang="ko">필드 길이가 초과되었습니다</span>    | The field length is over |
| <span id="code-a148">A148</span> | <span lang="ko">빌링 승인은 별도 API 이용 바랍니다</span>    | Billing approval should be made through a separate API |
| <span id="code-a149">A149</span> | <span lang="ko">빌링 승인만 가능한 API 입니다.(일반결제는 별도 API이용)</span>    | API for billing approval only |
| <span id="code-a150">A150</span> | <span lang="ko">ARS 이용 가맹점이 아닙니다</span>    | Not an ARS merchant |
| <span id="code-a201">A201</span> | <span lang="ko">PID 생성이 누락되었습니다</span> | PID generation is missing |
| <span id="code-a202">A202</span> | <span lang="ko">결과코드 생성이 누락되었습니다</span> | Result code generation is missing |
| <span id="code-a203">A203</span> | <span lang="ko">결과메시지 생성이 누락되었습니다</span>  | Result message generation is missing |
| <span id="code-a204">A204</span> | <span lang="ko">전문 복호화 오류가 발생하였습니다</span> | Decryption error occurred |
| <span id="code-a205">A205</span> | <span lang="ko">전문 암호화 오류가 발생하였습니다</span> | Encryption error occurred |
| <span id="code-a210">A210</span> | <span lang="ko">인증 요청내역이 존재하지 않습니다</span> | Authentication request information does not exist |
| <span id="code-a211">A211</span> | <span lang="ko">해쉬값 검증에 실패하였습니다</span>  |  Hash value verification failed |
| <span id="code-a212">A212</span> | <span lang="ko">잘못된 데이터 형식입니다</span>  | Invalid data format |
| <span id="code-a213">A213</span> | <span lang="ko">API 초기화 오류</span>   | API initialization error |
| <span id="code-a214">A214</span> | <span lang="ko">해당하는 카드거래가 없습니다</span>  |  No corresponding card transaction |
| <span id="code-a215">A215</span> | <span lang="ko">해당하는 핸드폰거래가 없습니다</span>    | No corresponding phone bill transaction |
| <span id="code-a216">A216</span> | <span lang="ko">해당하는 계좌이체거래가 없습니다</span>  | No corresponding bank transfer transaction |
| <span id="code-a217">A217</span> | <span lang="ko">해당하는 가상계좌거래가 없습니다</span>  | No corresponding virtual account transaction |
| <span id="code-a218">A218</span> | <span lang="ko">해당하는 전자상품권거래가 없습니다</span>    | No corresponding electronic gift certificate transaction |
| <span id="code-a219">A219</span> | <span lang="ko">환율정보 설정 오류 입니다</span> |  Exchange rate information configuration error |
| <span id="code-a220">A220</span> | <span lang="ko">설정된 환율 정보가 없습니다</span>   | No configured exchange rate information |
| <span id="code-a221">A221</span> | <span lang="ko">노티 수신정보 없음</span>    | No notification receiving information |
| <span id="code-a222">A222</span> | <span lang="ko">카드코드가 일치하지 않습니다</span>  | Card code does not match |
| <span id="code-a223">A223</span> | <span lang="ko">billKey 정보를 찾을 수 없습니다</span>   | Unable to find billKey information |
| <span id="code-a224">A224</span> | <span lang="ko">허용되지 않은 IP입니다</span>    | Unauthorized IP |
| <span id="code-a225">A225</span> | <span lang="ko">TID 중복 오류</span>  |  Duplicate TID error |
| <span id="code-a226">A226</span> | <span lang="ko">전문통신 과정에서 오류가 발생하였습니다</span>   | Error occurred during transaction |
| <span id="code-a227">A227</span> | <span lang="ko">필드명 중복 발생</span>   | Duplicate field name |
| <span id="code-a241">A241</span> | <span lang="ko">VISA3D 인증을 이용할 수 없는 카드 입니다</span>  | Card not eligible for VISA3D authentication |
| <span id="code-a242">A242</span> | <span lang="ko">정의되지 않은 지불수단 입니다</span> | Undefined payment method | 
| <span id="code-a243">A243</span> | <span lang="ko">주문정보가 존재하지 않습니다</span>  |  Order information does not exist |
| <span id="code-a244">A244</span> | <span lang="ko">주문내역 갱신에 실패하였습니다</span>    | Failed to update order history |
| <span id="code-a245">A245</span> | <span lang="ko">인증 시간이 초과 되었습니다</span>   | Authentication time has expired |
| <span id="code-a246">A246</span> | <span lang="ko">비정상 과다접속으로 인한 오류입니다</span>   | Error due to abnormal excessive access |
| <span id="code-a247">A247</span> | <span lang="ko">정의되지 않은 통화코드 입니다</span> | Undefined currency code |
| <span id="code-a248">A248</span> | <span lang="ko">사용할 수 없는 화폐 단위 입니다.(계약정보 확인필요)</span>    | Unusable currency unit (Contract information confirmation required) |
| <span id="code-a249">A249</span> | <span lang="ko">주문정보저장 실패(ORDER_DATA 최대 길이 초과)</span>   | Failed to save order information (ORDER_DATA exceeded maximum length) |
| <span id="code-a250">A250</span> | <span lang="ko">휴대폰번호 변조 오류(요청 번호와 인증완료 번호 상이)</span>   | Mobile phone number tampering error |
| <span id="code-a251">A251</span> | <span lang="ko">거래내역이 존재하지 않습니다.</span>  | No transaction history exists |
| <span id="code-a252">A252</span> | <span lang="ko">Signature 생성에 실패하였습니다.</span>   | Failed to generate signature |
| <span id="code-a253">A253</span> | <span lang="ko">빌키(BID) 생성에 실패하였습니다.</span>   | Failed to generate BID |
| <span id="code-a254">A254</span> | <span lang="ko">제휴사 응답전문이 유효하지 않습니다.</span>   | Invalid response message from partner |
| <span id="code-a255">A255</span> | <span lang="ko">타 상점 빌키(BID) 삭제 불가.</span>   |  Unable to delete BID of another store | 
| <span id="code-a256">A256</span> | <span lang="ko">ARS인증번호생성실패. 잠시후 재시도 바랍니다.</span>   | Failed to generate ARS authentication number. Please try again later |
| <span id="code-a257">A257</span> | <span lang="ko">주문정보 저장에 실패하였습니다.</span>    | Failed to save order information |
| <span id="code-a258">A258</span> | <span lang="ko">SMS 발송에 실패하였습니다.</span> | Failed to send SMS |
| <span id="code-a299">A299</span> | <span lang="ko">API 지연처리 발생.</span> | API transaction delay occurred |
| <span id="code-a300">A300</span> | <span lang="ko">기준정보 조회오류.</span> | Error occurred while querying information |
| <span id="code-a301">A301</span> | <span lang="ko">가맹점키 조회 오류입니다.</span>  |  Error occurred while querying merchant key |
| <span id="code-a302">A302</span> | <span lang="ko">기준정보상 필수 설정 정보가 없습니다.</span>  | Required setting information is missing |
| <span id="code-a303">A303</span> | <span lang="ko">기준정보 CPID 설정 정보가 없습니다.</span> | CPID setting information is missing |
| <span id="code-a304">A304</span> | <span lang="ko">DB테이블 INSERT 오류발생.</span>  | DB table INSERT error occurred |
| <span id="code-a305">A305</span> | <span lang="ko">가맹점번호 기준정보 미설정 오류.</span>   |  Error occurred because merchant's reference information is not set |
| <span id="code-a306">A306</span> | <span lang="ko">DB테이블 UPDATE 실패.</span>  | DB table UPDATE failed |
| <span id="code-a307">A307</span> | <span lang="ko">기준정보 조회 결과 2행 이상 오류</span>   | Error occurred because reference information query result is more than 2 rows |
| <span id="code-a400">A400</span> | <span lang="ko">대외기관 전문생성에 실패하였습니다.</span> | Failed to generate external organization transaction message |
| <span id="code-a401">A401</span> | <span lang="ko">대외계 전문통신 과정에서 오류가 발생하였습니다.</span> | Error occurred during external system transaction |
| <span id="code-a402">A402</span> | <span lang="ko">DB트랜잭션 처리에 실패하였습니다.</span>  | Failed to process DB transaction |
| <span id="code-a403">A403</span> | <span lang="ko">대외기관 응답 전문이 올바르지 않습니다.</span>    | External organization response message is invalid |
| <span id="code-9000">9000</span> | <span lang="ko">"누락된 필드명" + 필드값이 누락되었습니다.</span> | "Missing field name" + field value is missing |
| <span id="code-9001">9001</span> | <span lang="ko">필드 길이가 잘못되었습니다.</span>    | Invalid field length |
| <span id="code-9002">9002</span> | <span lang="ko">Try-Catch-Exception:"Exception 내용"</span>   | Try-Catch-Exception: "Exception contents" |
| <span id="code-s999">S999</span> | <span lang="ko">기타오류가 발생하였습니다.</span> | Other errors have occurred |
| <span id="code-s001">S001</span> | <span lang="ko">요청템플릿이 존재하지 않습니다.</span>    | Request template does not exist |
| <span id="code-s002">S002</span> | <span lang="ko">응답템플릿이 존재하지 않습니다.</span>    | Response template does not exist |
| <span id="code-t001">T001</span> | <span lang="ko">수신메시지 인코딩 중 예외가 발생하였습니다</span> | Encoding error is found in received data | 
| <span id="code-t002">T002</span> | <span lang="ko">비정상적인 수신 전문입니다</span> | Abnormal data is received |
| <span id="code-t003">T003</span> | <span lang="ko">수신데이터 파싱 중 예외가 발생하였습니다</span> | An exception occurred while parsing the received data |
| <span id="code-t004">T004</span> | <span lang="ko">요청 전문의 헤더부 생성 중 오류가 발생하였습니다</span> | An error occurred while generating the request header |
| <span id="code-t005">T005</span> | <span lang="ko">요청 전문의 바디부 생성 중 오류가 발생하였습니다</span> | An error occurred while generating the request body |
| <span id="code-x001">X001</span> | <span lang="ko">서버 도메인명이 잘못 설정되었습니다</span> | The server domain name is set incorrectly |
| <span id="code-x002">X002</span> | <span lang="ko">서버로 소켓 연결 중 오류가 발생하였습니다</span> | An error occurred during socket connection to the server |
| <span id="code-x003">X003</span> | <span lang="ko">전문 수신 중 오류가 발생하였습니다</span> | An error occurred while receiving the message |
| <span id="code-x004">X004</span> | <span lang="ko">전문 송신 중 오류가 발생하였습니다</span> | An error occurred while sending the message |
| <span id="code-v005">V005</span> | <span lang="ko">지원하지 않는 지불수단입니다</span> | This payment method is not supported |
| <span id="code-v101">V101</span> | <span lang="ko">암호화 플래그 미설정 오류입니다</span> | Encryption flag is not set |
| <span id="code-v102">V102</span> | <span lang="ko">서비스모드를 설정하지 않았습니다</span> | Service mode is not set |
| <span id="code-v103">V103</span> | <span lang="ko">지불수단을 설정하지 않았습니다</span> | Payment methods is not set |
| <span id="code-v104">V104</span> | <span lang="ko">상품개수 미설정 오류입니다</span> | Product number is not set |
| <span id="code-v201">V201</span> | <span lang="ko">상점ID 미설정 오류입니다</span> | Merchant ID is not set |
| <span id="code-v202">V202</span> | <span lang="ko">LicenseKey 미설정 오류입니다</span> | LicenseKey is not set |
| <span id="code-v203">V203</span> | <span lang="ko">통화구분 미설정 오류입니다</span> | Currency type is not set |
| <span id="code-v204">V204</span> | <span lang="ko">MID 미설정 오류입니다</span> | MID is not set |
| <span id="code-v205">V205</span> | <span lang="ko">MallIP 미설정 오류입니다</span> | MallIP is not set |
| <span id="code-v301">V301</span> | <span lang="ko">구매자이름 미설정 오류입니다</span> | Buyer name is not set |
| <span id="code-v302">V302</span> | <span lang="ko">구매자인증번호 미설정 오류입니다</span> | Buyer verification number is not set |
| <span id="code-v303">V303</span> | <span lang="ko">구매자연락처 미설정 오류입니다</span> | Buyer phone number is not set |
| <span id="code-v304">V304</span> | <span lang="ko">구매자메일주소 미설정 오류입니다</span> | Buyer email address is not set |
| <span id="code-v401">V401</span> | <span lang="ko">상품명 미설정 오류입니다</span> | Product name not set |
| <span id="code-v402">V402</span> | <span lang="ko">상품금액 미설정 오류입니다</span> | Product amount is not set |
| <span id="code-v501">V501</span> | <span lang="ko">카드형태 미설정 오류입니다</span> | Card type is not set |
| <span id="code-v502">V502</span> | <span lang="ko">카드구분 미설정 오류입니다</span> | Card is not set |
| <span id="code-v503">V503</span> | <span lang="ko">카드코드 미설정 오류입니다</span> | Card code is not set |
| <span id="code-v504">V504</span> | <span lang="ko">카드번호 미설정 오류입니다</span> | Card number is not set |
| <span id="code-v505">V505</span> | <span lang="ko">카드무이자여부 미설정 오류입니다</span> | Interest-free type is not set |
| <span id="code-v506">V506</span> | <span lang="ko">카드인증구분 미설정 오류입니다</span> | Card authentication type is not set |
| <span id="code-v507">V507</span> | <span lang="ko">카드형태 설정 오류입니다</span> | Card type setting |
| <span id="code-v508">V508</span> | <span lang="ko">카드형태 허용하지 않는 값을 설정하였습니다</span> | Not allowed card type |
| <span id="code-v509">V509</span> | <span lang="ko">카드구분 허용하지 않는 값을 설정하였습니다</span> | Not allowed card type |
| <span id="code-v510">V510</span> | <span lang="ko">유효기간 미설정 오류입니다</span> | Expiration date is not set |
| <span id="code-v511">V511</span> | <span lang="ko">유효기간 허용하지 않는 값을 설정하였습니다</span> | Not allowed expiration value |
| <span id="code-v512">V512</span> | <span lang="ko">유효기간의 월 형태가 잘못 설정되었습니다</span> | Expiration month format |
| <span id="code-v513">V513</span> | <span lang="ko">카드 비밀번호 미입력 오류입니다</span> | Card password is not entered |
| <span id="code-v601">V601</span> | <span lang="ko">은행코드 미설정 오류입니다</span> | Bank code is not set |
| <span id="code-v602">V602</span> | <span lang="ko">금융결제원 암호화 데이터 미설정 오류입니다</span> | KFTC encrypted data is not set |
| <span id="code-v701">V701</span> | <span lang="ko">가상계좌입금만료일 미설정 오류입니다</span> | Virtual account deposit expiration date is not set |
| <span id="code-va01">VA01</span> | <span lang="ko">거래KEY 미설정 오류입니다</span> | Transaction key is not set | 
| <span id="code-va02">VA02</span> | <span lang="ko">이통사구분 미설정 오류입니다</span> | Carrier is not set |
| <span id="code-va03">VA03</span> | <span lang="ko">SMS승인번호 미설정 오류입니다</span> | SMS authorization number not set |
| <span id="code-va04">VA04</span> | <span lang="ko">업체TID 미설정 오류입니다</span> | TID is not set |
| <span id="code-va05">VA05</span> | <span lang="ko">휴대폰번호 미설정 오류입니다</span> | Mobile number is not set |
| <span id="code-va09">VA09</span> | <span lang="ko">고객고유번호(주민번호,사업자번호) 미설정 오류입니다</span> | Customer unique number (resident number, business number) not set |
| <span id="code-va10">VA10</span> | <span lang="ko">ENCODE 업체TID 미설정   오류입니다</span> | ENCODE Company TID is not set |
| <span id="code-vb02">VB02</span> | <span lang="ko">이통사구분 미설정 오류입니다</span> | Carrier is not set |
| <span id="code-vb05">VB05</span> | <span lang="ko">휴대폰번호 미설정 오류입니다</span> | Phone number is not set |
| <span id="code-vb09">VB09</span> | <span lang="ko">고객고유번호(주민번호,사업자번호) 미설정 오류입니다</span> | Customer unique number (resident number, business number) not set |
| <span id="code-vb10">VB10</span> | <span lang="ko">고객 IP 미설정 오류입니다</span> | Customer IP is not configured |
| <span id="code-v801">V801</span> | <span lang="ko">취소금액 미설정 오류입니다</span>  | Cancellation amount is not set |
| <span id="code-v802">V802</span> | <span lang="ko">취소사유 미설정 오류입니다</span> | Cancel reason is not set |
| <span id="code-v803">V803</span> | <span lang="ko">취소패스워드 미설정 오류입니다</span> | Cancel password is not set |
| <span id="code-p001">P001</span> | <span lang="ko">클라이언트 아이디가 없습니다.</span>    | Missing Client ID       |
| <span id="code-p002">P002</span> | <span lang="ko">토큰생성을 실패하였습니다.</span>       | Failed to Generate Token       |
| <span id="code-p003">P003</span> | <span lang="ko">로그추적아이디 생성을 실패하였습니다.</span>   | Failed to Generate Log Trace ID       |
| <span id="code-p004">P004</span> | <span lang="ko">SID가 생성되지 않았습니다.</span>       | SID not Generated       |
| <span id="code-p005">P005</span> | <span lang="ko">해당되는 MID 정보가 없습니다.</span>    | No MID Information Found       |
| <span id="code-p006">P006</span> | <span lang="ko">해당되는 GID 정보가 없습니다.</span>    | No GID Information Found       |
| <span id="code-p007">P007</span> | <span lang="ko">필수 파라미터 {param}가 없습니다.</span>      | Required Parameter {param} Missing    |
| <span id="code-p008">P008</span> | <span lang="ko">파라미터 {}:{} 길이가 취소길이{} 보다 작은값 입니다.</span>  | The length of parameter {}:{} is below the minimum length {}      |
| <span id="code-p009">P009</span> | <span lang="ko">파라미터 {}:{} 길이가 최대길이{} 보다 큰값 입니다.</span>    | Parameter {}:{} length is greater than Maximum Length {}   |
| <span id="code-p010">P010</span> | <span lang="ko">파라미터 {key}[{value}]가 숫자형식이 아닙니다.</span>       | Parameter {key}[{value}] is not a numeric format    |
| <span id="code-p011">P011</span> | <span lang="ko">파라미터 {key}[{value}]가 boolean형식이 아닙니다.</span>    | Parameter {key}[{value}] is not a boolean format    |
| <span id="code-p012">P012</span> | <span lang="ko">파라미터 {key}[{value}]는 {} 값만 허용 합니다.</span>       | Parameter {key}[{value}] only allows {} value       |
| <span id="code-p013">P013</span> | <span lang="ko">파라미터 {}[{}]이 공급가액, 부가세, 봉사료, 비과세급액의 합과 동일하지 않습니다.</span> | Parameter {}[{}] does not match the sum of taxable supply, tax, service fee, and non-taxable amount |
| <span id="code-p014">P014</span> | <span lang="ko">Mid [{}]에 해당 하는 merchant key 정보가 없습니다.</span>    | No merchant key information found for MID [{}]      |
| <span id="code-p015">P015</span> | <span lang="ko">orderid {}가 이미 존재합니다.</span>    | Order ID {} already exists     |
| <span id="code-p016">P016</span> | <span lang="ko">파라미터로 전달된 MID {}에 대한 사용이 불가 합니다.</span>   | The use of MID {} passed as a parameter is not available   |
| <span id="code-p017">P017</span> | <span lang="ko">결제금액은 0원 결제가 불가합니다.</span>      | Payment of 0 won is not available     |
| <span id="code-p018">P018</span> | <span lang="ko">인증 응답 토큰 데이터가 없습니다.</span>      | No authentication response token data        |
| <span id="code-p019">P019</span> | <span lang="ko">요청된 인증 정보가 없습니다. Token [{}].</span>      | Authentication information requested does not exist. Token [{}].  |
| <span id="code-p020">P020</span> | <span lang="ko">네이버페이 easyPayMethod 데이터 확인이 필요합니다.</span>    | Naver Pay easyPayMethod data needs to be checked.   |
| <span id="code-p021">P021</span> | <span lang="ko">지원 카드가 아닙니다.</span>     | Unsupported card.       |
| <span id="code-p022">P022</span> | <span lang="ko">카드와 할부가 동시에 설정되어야 합니다.</span>       | The card and installment should be set at the same time.   |
| <span id="code-p023">P023</span> | <span lang="ko">간편결제는 [{}]와 함께 사용이 불가합니다.</span>     | Easy payment cannot be used with [{}].       |
| <span id="code-p024">P024</span> | <span lang="ko">날짜형식(ISO8601)이 아닙니다.</span>    | Invalid date format (ISO8601).        |
| <span id="code-p025">P025</span> | <span lang="ko">트랜잭션 타입이 일치하지 않습니다.</span>      | Transaction type does not match.      |
| <span id="code-p026">P026</span> | <span lang="ko">승인서버 요청 에러</span>        | Approval server request error.        |
| <span id="code-p027">P027</span> | <span lang="ko">원격 실패(MCI)</span>     | Remote failure.    |
| <span id="code-p028">P028</span> | <span lang="ko">일시불 01 에러 (일시불은 00으로 설정)</span>   | Installment error for single payment 01 (set to 00 for single payment).  |
| <span id="code-p029">P029</span> | <span lang="ko">해당 지불수단은 카드 지정, 할부 지정이 불가 합니다.</span>   | The payment method cannot be designated for card or installment.  |
| <span id="code-p030">P030</span> | <span lang="ko">해당 지불수단은 카드 지정, 할부 지정이 필수 입니다.</span>   | Card and installment designation is required for this payment method.    |
| <span id="code-p031">P031</span> | <span lang="ko">가상계좌 유효시간 지정 오류 입니다.</span>     | Error in specifying the virtual account validity period.   |
| <span id="code-p032">P032</span> | <span lang="ko">다이렉트 및 간편결제는 에스크로 이용이 불가 합니다.</span>   | Direct and easy payment cannot be used with escrow.       |
| <span id="code-p033">P033</span> | <span lang="ko">간편결제는 다중카드 선택이 불가합니다.</span>  | Multiple card selection is not allowed for easy payment.   |
| <span id="code-p034">P034</span> | <span lang="ko">요청된 금액과 내역 금액이 일치하지 않습니다.</span>   | The requested amount and the transaction amount do not match.     |
| <span id="code-p035">P035</span> | <span lang="ko">망취소 요청 오류 입니다.</span>  | Error in network cancellation request.       |
| <span id="code-p036">P036</span> | <span lang="ko">면세금액이 결제금액을 초과할수 없습니다.</span>      | Tax-free amount cannot exceed the payment amount.   |
| <span id="code-p037">P037</span> | <span lang="ko">5만원 미만인 경우 할부 지정이 불가 합니다.</span>     | Installment cannot be designated if the amount is less than 50,000 won.  |
| <span id="code-p038">P038</span> | <span lang="ko">요청전문 검증오류 입니다.</span>       | Request message verification error.   |
| <span id="code-p039">P039</span> | <span lang="ko">세션키는 필수 값입니다.</span>   | Session key is a required value.      |
| <span id="code-p040">P040</span> | <span lang="ko">세션키에 해당하는 주문정보가 존재하지 않습니다.</span>      | There is no order information corresponding to the session key.   |
| <span id="code-p041">P041</span> | <span lang="ko">세션키 길이가 잘못되었습니다.</span>    | Session key length is incorrect.      |
| <span id="code-p042">P042</span> | <span lang="ko">이미 만료된 세션 아이디 입니다.</span>  | The session ID has already expired.   |
| <span id="code-p043">P043</span> | <span lang="ko">유효하지 않은 세션 아이디 입니다.</span>      | Invalid session ID.     |
| <span id="code-p044">P044</span> | <span lang="ko">승인 상태 수정에 실패하였습니다.</span>       | Failed to modify approval status.     |
| <span id="code-p045">P045</span> | <span lang="ko">이미 처리된 거래건 입니다.</span> | Transaction has already been processed. |
| <span id="code-p047">P047</span> | <span lang="ko">이미 처리된 상태[{0}]의 세션 아이디 입니다.</span> | Session ID in state [{0}] has already been processed. |
| <span id="code-p049">P049</span> | <span lang="ko">결제 승인 처리 중입니다. 완료 후 결과를 확인해주세요.</span> | Payment authorization is in progress. Please check the result once it is completed. |
| <span id="code-p101">P101</span> | <span lang="ko">DB 트랜잭션 실패</span>   | Database transaction failed.   |
| <span id="code-p102">P102</span> | <span lang="ko">auth history 데이터 추가 실패하였습니다.</span>      | Failed to add authentication history data.   |
| <span id="code-p103">P103</span> | <span lang="ko">log trace 데이터 추가 실패하였습니다.</span>   | Failed to add log trace data.         |
| <span id="code-p091">P091</span> | <span lang="ko">결제 요청을 취소하였습니다.</span>      | Payment request has been canceled.    |
| <span id="code-u100">U100</span> | <span lang="ko">{0} 필수입력항목이 누락되었습니다.</span>      | {0} is a required field that is missing.     |
| <span id="code-u101">U101</span> | <span lang="ko">BASE64 DECODE 실패</span>        | Failed to decode BASE64.       |
| <span id="code-u102">U102</span> | <span lang="ko">사용자 인증정보가 존재하지 않습니다.</span>    | User authentication information does not exist.     |
| <span id="code-u103">U103</span> | <span lang="ko">사용자 인증타입이 맞지 않습니다.</span>       | User authentication type is incorrect.       |
| <span id="code-u104">U104</span> | <span lang="ko">사용자 인증에 실패하였습니다.</span>    | User authentication failed.    |
| <span id="code-u105">U105</span> | <span lang="ko">필드 최대길이 초과[max:{0}, realLength:{1}]</span>    | Maximum field length exceeded [max: {0}, realLength: {1}].       |
| <span id="code-u106">U106</span> | <span lang="ko">현금영수증 취소는 별도 API 이용 요망</span>    | Cash receipt cancellation requires a separate API usage.   |
| <span id="code-u107">U107</span> | <span lang="ko">거래내역이 존재 하지 않습니다.</span>   | Transaction history does not exist.   |
| <span id="code-u108">U108</span> | <span lang="ko">허가되지 않은 요청 입니다.</span>       | Unauthorized request.    |
| <span id="code-u109">U109</span> | <span lang="ko">허용되지 않은 요청 입니다.</span>       | Request not allowed.    |
| <span id="code-u110">U110</span> | <span lang="ko">지원하지 않는 지불수단 입니다.</span>   | Unsupported payment method.    |
| <span id="code-u111">U111</span> | <span lang="ko">조회내용이 없습니다.</span>      | No query content.       |
| <span id="code-u112">U112</span> | <span lang="ko">이미 사용된 OrderId 입니다.</span>      | OrderId already used.    |
| <span id="code-u113">U113</span> | <span lang="ko">빌키 승인 금액 불일치</span>     | Inconsistent bill key approval amount.       |
| <span id="code-u114">U114</span> | <span lang="ko">이미 사용된 TID 입니다.</span>   | TID already used.       |
| <span id="code-u115">U115</span> | <span lang="ko">삭제 처리된 BID 입니다.</span>   | Deleted BID.     |
| <span id="code-u116">U116</span> | <span lang="ko">사용자 정보가 존재하지 않습니다.</span>       | User information does not exist.      |
| <span id="code-u117">U117</span> | <span lang="ko">사용자 비밀번호가 일치하지 않습니다.</span>    | User password is incorrect.    |
| <span id="code-u118">U118</span> | <span lang="ko">이용 불가한 사용자 정보 입니다.</span>  | Unusable user information.     |
| <span id="code-u119">U119</span> | <span lang="ko">지원하지 않는 지불수단입니다.</span>    | Unsupported payment method.    |
| <span id="code-u120">U120</span> | <span lang="ko">TID가 유효하지 않습니다.</span>  | Invalid TID.     |
| <span id="code-u121">U121</span> | <span lang="ko">인증 요청내역이 존재하지 않습니다.</span>      | Authentication request history does not exist.      |
| <span id="code-u122">U122</span> | <span lang="ko">취소 해당거래 없음</span>        | No transaction to cancel.      |
| <span id="code-u123">U123</span> | <span lang="ko">취소금액이 취소가능금액보다 큼.</span>  | Cancel amount is greater than cancelable amount.    |
| <span id="code-u124">U124</span> | <span lang="ko">필드 길이가 잘못되었습니다.</span>      | Field length is incorrect.     |
| <span id="code-u125">U125</span> | <span lang="ko">잘못된 요청 입니다.</span>       | Invalid request.        |
| <span id="code-u126">U126</span> | <span lang="ko">orderId가 존재 하지 않습니다.</span>    | OrderId does not exist.        |
| <span id="code-u127">U127</span> | <span lang="ko">요청 금액이 올바르지 않습니다.</span>   | Request amount is invalid.     |
| <span id="code-u128">U128</span> | <span lang="ko">부분취소는 운영 환경에서 이용 가능(샌드박스는 부분취소 미제공)</span>     | Partial cancellation is only available in production environment (not supported in sandbox). |
| <span id="code-u129">U129</span> | <span lang="ko">잘못된 요청 기간 입니다.</span>  | Invalid request period.        |
| <span id="code-u130">U130</span> | <span lang="ko">허용된 Date형식이 아닙니다.</span>      | Invalid Date format.    |
| <span id="code-u131">U131</span> | <span lang="ko">허용된 Data형식이 아닙니다.</span>      | Invalid Data format.    |
| <span id="code-u132">U132</span> | <span lang="ko">허용된 옵션 내용이 아닙니다.[{0}]</span>      | Invalid option content. [{0}]         |
| <span id="code-u133">U133</span> | <span lang="ko">요청 파라미터의 형식이 잘못되었습니다.[{0}]</span>      | Invalid request parameter format. [{0}]         |
| <span id="code-u134">U134</span> | <span lang="ko">[{0}]만원 미만인 경우 할부 지정이 불가 합니다.</span>      | Installment is not allowed for amounts under [{0}] (in units of 10,000 KRW).         |
| <span id="code-u135">U135</span> | <span lang="ko">해당 지불수단은 카드 지정, 할부 지정이 필수 입니다.</span>      | For this payment method, specifying a card company and installment month is required.         |
| <span id="code-u136">U136</span> | <span lang="ko">간편결제는 다중카드 선택이 불가합니다.</span>      | Easy Pay does not support selecting multiple cards.         |
| <span id="code-u138">U138</span> | <span lang="ko">간편결제수단에서 cardQuota 사용시, cardCode 필수 입니다.</span>      | When using cardQuota with an Easy Pay method, cardCode is required.         |
| <span id="code-u139">U139</span> | <span lang="ko">페이결제는 카드사 선택이 불가합니다.</span>      | This Easy Pay method does not support selecting a card company.         |
| <span id="code-u140">U140</span> | <span lang="ko">페이결제는 할부개월 수 지정이 불가합니다.</span>      | This Easy Pay method does not support specifying an installment month.         |
| <span id="code-u141">U141</span> | <span lang="ko">해당 결제수단은 할부개월 수 복수설정이 불가합니다.</span>      | This payment method does not support setting multiple installment months.         |
| <span id="code-u142">U142</span> | <span lang="ko">[{0}] 필드는 0이하의 값은 허용하지 않습니다.</span>      | The [{0}] field does not allow a value of 0 or less. Returned by Cancel for a `cancelAmt` of 0 or less         |
| <span id="code-u143">U143</span> | <span lang="ko">[{0}] 결제수단에서는 [{1}] 필드 사용이 불가합니다.</span>      | The [{1}] field cannot be used with the [{0}] payment method.         |
| <span id="code-u144">U144</span> | <span lang="ko">할부 개월 수는 최대 [{0}] 개월 입니다.</span>      | The maximum installment period is [{0}] months.         |
| <span id="code-u145">U145</span> | <span lang="ko">[{0}] 결제수단에서는 해당 카드사 번호 사용이 불가합니다.</span>      | This card company code cannot be used with the [{0}] payment method.         |
| <span id="code-u146">U146</span> | <span lang="ko">휴대폰 결제는 에스크로 이용이 불가 합니다.</span>      | Escrow is not available for mobile phone payments.         |
| <span id="code-u147">U147</span> | <span lang="ko">허용되지 않은 트랜젝션 타입입니다.</span>      | This transaction type is not allowed.         |
| <span id="code-u148">U148</span> | <span lang="ko">directReceiptType 선택 시, directReceiptNo는 필수 입니다.</span>      | directReceiptNo is required when directReceiptType is selected.         |
| <span id="code-u149">U149</span> | <span lang="ko">유효하지 않은 카드사 번호입니다.</span>      | Invalid card company code.         |
| <span id="code-u150">U150</span> | <span lang="ko">해당 결제수단에서 허용되지 않은 결제타입 입니다.</span>      | This billing type is not allowed for this payment method.         |
| <span id="code-u151">U151</span> | <span lang="ko">가상계좌 만료일자(vbankExpDate)는 ISO 8601 형식이어야 합니다. 예: 2025-12-31 또는 2025-12-31T23:59:59</span>      | The virtual account expiration date (vbankExpDate) must be in ISO 8601 format. Ex) 2025-12-31 or 2025-12-31T23:59:59         |
| <span id="code-u152">U152</span> | <span lang="ko">가상계좌 만료일자(vbankExpDate)는 현재 시간보다 미래여야 합니다.</span>      | The virtual account expiration date (vbankExpDate) must be later than the current time.         |
| <span id="code-u153">U153</span> | <span lang="ko">가상계좌 만료일자(vbankExpDate) 형식이 올바르지 않습니다: [{0}]</span>      | The virtual account expiration date (vbankExpDate) format is invalid: [{0}]         |
| <span id="code-u154">U154</span> | <span lang="ko">[{0}]에 공백 문자가 포함되어 있습니다. 공백 없이 입력해주세요.</span>      | [{0}] contains a whitespace character. Please enter it without spaces.         |
| <span id="code-u301">U301</span> | <span lang="ko">ORDER_DATA 최대 길이 초과.</span>       | Exceeded maximum length of ORDER_DATA.       |
| <span id="code-u302">U302</span> | <span lang="ko">응답전문 최대 길이 초과.</span>  | Exceeded maximum length of response message.       |
| <span id="code-u303">U303</span> | <span lang="ko">API 지연처리 발생.</span>        | API delay occurred.     |
| <span id="code-u304">U304</span> | <span lang="ko">BASIC AUTHENTICATION 실패</span>       | Failed to authenticate using BASIC authentication.  |
| <span id="code-u305">U305</span> | <span lang="ko">BEARER AUTHENTICATION 실패</span>       | Failed to authenticate using BEARER authentication.       |
| <span id="code-u306">U306</span> | <span lang="ko">전자서명 및 암호화메시지 검증 실패</span>      | Failed to verify electronic signature and encrypted message.     |
| <span id="code-u307">U307</span> | <span lang="ko">인증정보 확인중 오류가 발생하였습니다.</span>  | An error occurred while verifying authentication information.     |
| <span id="code-u308">U308</span> | Method Not Allowed.       | Method Not Allowed.     |
| <span id="code-u309">U309</span> | <span lang="ko">발급되지 않은 BID 입니다.</span>       | BID not issued.         |
| <span id="code-u310">U310</span> | <span lang="ko">SIGN DATA 생성에 실패하였습니다.</span>       | Failed to generate SIGN DATA.         |
| <span id="code-u311">U311</span> | <span lang="ko">인증이 취소되었거나 실패하였습니다. 다시 시도하여 주십시요.</span>  | Authentication has been canceled or failed. Please try again.     |
| <span id="code-u312">U312</span> | <span lang="ko">SIGN DATA 검증에 실패하였습니다.</span>       | Failed to verify SIGN DATA.    |
| <span id="code-u313">U313</span> | <span lang="ko">상점 MID가 유효하지 않습니다.</span>    | Invalid MID for the store.     |
| <span id="code-u314">U314</span> | <span lang="ko">전문 암호화 오류가 발생하였습니다.</span>      | An error occurred during message encryption.       |
| <span id="code-u315">U315</span> | <span lang="ko">허용되지 않은 IP 입니다.</span>  | Invalid IP address.     |
| <span id="code-u316">U316</span> | <span lang="ko">상점 기준정보가 유효하지 않습니다.</span>      | Invalid merchant information.         |
| <span id="code-u317">U317</span> | <span lang="ko">인증정보 확인중 오류가 발생하였습니다.</span>  | Error occurred while verifying authentication information.       |
| <span id="code-u318">U318</span> | <span lang="ko">취소 금액 불일치</span>   | Cancellation amount mismatch.         |
| <span id="code-u319">U319</span> | <span lang="ko">요청하신 면세금액이 취소가능한 면세금액을 초과 오류</span>   | Requested tax-free amount exceeds the cancellable tax-free amount error.       |
| <span id="code-u320">U320</span> | <span lang="ko">페이지를 찾을 수 없습니다.</span>       | Page not found.         |
| <span id="code-u321">U321</span> | <span lang="ko">면세금액이 결제금액 보다 큼.</span>     | Tax-free amount is greater than payment amount.     |
| <span id="code-u322">U322</span> | <span lang="ko">세션아이디 생성을 실패 하였습니다.</span>      | Failed to generate session ID.        |
| <span id="code-u323">U323</span> | <span lang="ko">세션아이디 만료 변경이 실패 하였습니다.</span>       | Failed to change session ID expiration.      |
| <span id="code-u324">U324</span> | <span lang="ko">이미 사용된 세션아이디 입니다.</span>   | Session ID already used.     |
| <span id="code-u325">U325</span> | <span lang="ko">이미 만료된 세션아이디 입니다.</span>   | Session ID already expired.    |
| <span id="code-u327">U327</span> | <span lang="ko">면세금액이 결제금액을 초과할수 없습니다.</span>   | The tax-free amount cannot exceed the payment amount.    |
| <span id="code-u328">U328</span> | <span lang="ko">파라미터 공급가액, 부가세, 봉사료, 비과세급액의 합과 동일하지 않습니다.</span>   | The sum of the supply amount, VAT, service charge, and tax-free amount does not match the payment amount.    |
| <span id="code-u329">U329</span> | <span lang="ko">파라미터가 숫자형식이 아닙니다.</span>   | The parameter is not in numeric format.    |
| <span id="code-u330">U330</span> | <span lang="ko">날짜형식(ISO8601)이 아닙니다.</span>   | The date is not in ISO 8601 format.    |
| <span id="code-u331">U331</span> | <span lang="ko">다이렉트 및 간편결제는 에스크로 이용이 불가 합니다.</span>   | Escrow is not available for direct payment or Easy Pay.    |
| <span id="code-u332">U332</span> | <span lang="ko">CardEasyPay는 cardCode(카드지정), cardQuota(할부지정)이 불가 합니다.</span> | cardCode and cardQuota cannot be used with the cardAndEasyPay method; other Easy Pay methods do accept them, see U136, U138 and U141 | 
| <span id="code-u333">U333</span> | <span lang="ko">이미 등록된 URL 정보가 있습니다.</span> | There is already registered URL information. | 
| <span id="code-u334">U334</span> | <span lang="ko">웹훅 URL 수정을 실패하였습니다.</span> | Failed to modify the webhook URL. | 
| <span id="code-u335">U335</span> | <span lang="ko">웹훅 URL 생성을 실패하였습니다.</span> | Failed to create the webhook URL. |
| <span id="code-u336">U336</span> | <span lang="ko">해당 URL 페이지 요청을 실패하였습니다.</span> | Failed to request the URL page. |
| <span id="code-u337">U337</span> | <span lang="ko">HTTP 상태 코드가 정상(200)이 아닙니다.</span> | The HTTP status code is not 200 |
| <span id="code-u338">U338</span> | <span lang="ko">응답 페이지 Body 부분은 OK 문자만 허용됩니다.</span> | Only the 'OK' string is allowed in the response body. |
| <span id="code-u339">U339</span> | <span lang="ko">[{0}] 필드는 0이하의 값은 허용하지 않습니다.</span> | The [{0}] field does not allow a value of 0 or less. Returned by Create checkout for a `vbankValidHours` of 0 or less |
| <span id="code-u340">U340</span> | <span lang="ko">KeyIn 결제정보 암호화 데이터 복호화오류</span> | Failed to decrypt the Key-in payment encryption data. |
| <span id="code-u341">U341</span> | <span lang="ko">KeyIn 결제정보 암호화 데이터 검증 오류</span> | Key-in payment encryption data verification error. |
| <span id="code-u343">U343</span> | <span lang="ko">가맹점 옵션값 조회 실패.</span> | Failed to retrieve the merchant option value. |
| <span id="code-u344">U344</span> | <span lang="ko">해당 결제 수단 OpenType : Redirect 불가</span> | Redirect is not available as the OpenType for this payment method. |
| <span id="code-u345">U345</span> | <span lang="ko">BID가 유효하지 않습니다.</span> | The BID is not valid. |
| <span id="code-u501">U501</span> | <span lang="ko">대외계 전문통신 과정에서 오류가 발생하였습니다.</span>      | Error occurred during external message communication process.     |
| <span id="code-u502">U502</span> | <span lang="ko">요청 금액이 올바르지 않습니다.</span>   | Invalid requested amount.      |
| <span id="code-u503">U503</span> | <span lang="ko">망취소 요청</span>        | Network cancellation request.         |
| <span id="code-u504">U504</span> | <span lang="ko">결제에 실패하였습니다.(망취소 처리)</span>     | Payment has failed. (Network cancellation processing)      |
| <span id="code-u506">U506</span> | <span lang="ko">DB 테이블 INSERT 오류발생.</span>       | Error occurred while inserting into DB table.       |
| <span id="code-u507">U507</span> | <span lang="ko">DB 테이블 UPDATE 실패.</span>    | Failed to update DB table.     |
| <span id="code-u508">U508</span> | <span lang="ko">서버로 소켓 연결 중 오류가 발생하였습니다.</span>     | Error occurred while connecting to server through socket.  |
| <span id="code-u509">U509</span> | <span lang="ko">기준정보 조회 결과 2행 이상 오류</span>       | Error occurred while retrieving reference information.     |
| <span id="code-u700">U700</span> | <span lang="ko">WEBHOOK 응답전문 최대 길이 초과.</span>       | Exceeded maximum length of webhook response.       |
| <span id="code-c001">C001</span> | <span lang="ko">ISP 인증이 취소되었거나 실패하였습니다 다시 시도하여 주십시요</span> | ISP authentication has been cancelled or failed. Please try again.        |
| C002 | <span lang="ko">카드사 인증 실패</span>    | Card company authentication failed (card authentication)            |
| <span id="code-i001">I001</span> | <span lang="ko">서버와의 통신에 실패하였습니다 네트워크 환경을 확인하세요</span>     | Failed to communicate with the server. Please check the network environment.          |
| <span id="code-i002">I002</span> | <span lang="ko">사용자가 결제를 취소하였습니다</span>           | The user has cancelled the payment.           |
| <span id="code-i003">I003</span> | <span lang="ko">인증 성공한 거래로 재요청 되었습니다 결제가 정상적으로 이루어 지지 않았을 경우 가맹점 페이지로 가서 다시 결제하여 주십시요</span> | The transaction has been resubmitted as the authentication was successful. If the payment was not completed successfully, please go to the merchant page and try again. |
| <span id="code-i004">I004</span> | <span lang="ko">인증 실패한 거래로 재요청 되었습니다 가맹점 페이지로 가서 다시 결제하여 주십시요</span>          | The transaction has been resubmitted as the authentication failed. Please go to the merchant page and try again.  |
| <span id="code-i006">I006</span> | <span lang="ko">화면 구성 처리에 실패하였습니다 가맹점 페이지로 가서 다시 결제하여 주십시요</span>      | Failed to process screen configuration. Please go to the merchant page and try again. |
| <span id="code-i007">I007</span> | <span lang="ko">바로 호출 결제요청 정보를 확인하십시오</span>   | Please check the payment request information that was directly called.    |
| <span id="code-m001">M001</span> | <span lang="ko">계좌이체 CPID를 확인해 주세요</span>            | Please check the transfer CPID.   |
| <span id="code-m002">M002</span> | <span lang="ko">CPID미설정 오류입니다</span>        | Error: CPID not set up.  |
| <span id="code-n001">N001</span> | <span lang="ko">30만원 이상 결제는 지원되지 않습니다</span>     | Payments over 300,000 won are not supported.  |
| <span id="code-n002">N002</span> | <span lang="ko">주문내역 갱신에 실패하였습니다</span>           | Failed to update order details.   |
| <span id="code-n003">N003</span> | <span lang="ko">인증기관 거래설정에 실패하였습니다 가맹점 페이지로 가서 다시 결제하여 주십시요</span>   | Failed to set up transaction with authentication institution. Please go to the merchant page and try again.       |
| <span id="code-s004">S004</span> | <span lang="ko">무이자 설정 정보 오류</span>        | Interest-free settings information error.     |
| <span id="code-s005">S005</span> | <span lang="ko">청구할인 행사 정보 오류</span>      | Billing discount event information error.     |
| <span id="code-v001">V001</span> | <span lang="ko">가맹점 아이디가 조회 되지 않습니다</span>       | Merchant ID cannot be found.      |
| <span id="code-v003">V003</span> | <span lang="ko">비정상 과다접속으로 인한 오류입니다 다시 결제 시도하시기 바랍니다</span>    | Error due to abnormal excessive access. Please try again to make the payment.         |
| <span id="code-v004">V004</span> | <span lang="ko">할부개월 설정 오류</span>  | Installment months setting error. |
| <span id="code-w000">W000</span> | <span lang="ko">정상 처리되었습니다</span>          | Processed normally.      |
| <span id="code-w001">W001</span> | <span lang="ko">주문번호가 유효하지 않습니다</span> | Invalid order number.    |
| <span id="code-w002">W002</span> | <span lang="ko">TID가 유효하지 않습니다</span>      | Invalid transaction ID   |

<br>
