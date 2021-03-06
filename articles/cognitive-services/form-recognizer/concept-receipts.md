---
title: 수신-양식 인식기
titleSuffix: Azure Cognitive Services
description: 인식기 API 사용 및 제한으로 수신 분석과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81e8cd5cf4af8da76ae4eb09bed5a4ee0368da4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467360"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>양식 인식기 미리 빌드된 수신 모델

Azure 양식 인식기는 미리 작성 된 수신 모델을 사용 하 여 판매 영수증에서 정보를 분석 하 고 추출할 수 있습니다. 강력한 [OCR (광학 인식)](../computer-vision/concept-recognizing-text.md) 기능을 심층 학습 모델과 결합 하 여 영어로 작성 된 수령에서 주요 정보를 추출 합니다.

## <a name="understanding-receipts"></a>수신 확인 이해

많은 기업과 개인은 여전히 판매 영수증에서 수동으로 추출 된 데이터를 사용 합니다. 이러한 수신 확인에서 데이터를 자동으로 추출 하는 것은 복잡할 수 있습니다. 수신 확인은 crumpled 하 고, 필기 하는 부분은 적고, 품질이 낮은 스마트폰 이미지를 포함 합니다. 또한 수신 템플릿 및 필드는 시장, 지역 및 판매자에 따라 크게 다를 수 있습니다. 이러한 데이터 추출 및 현장 검색 문제는 고유한 문제를 확인 하는 데 사용 됩니다.  

수신 API는 OCR (광학 문자 인식) 및 미리 작성 된 모델을 사용 하 여 방대한 수신 처리 시나리오를 가능 하 게 합니다. 수신 API를 사용 하면 모델을 학습 하지 않아도 됩니다. 수신 확인 이미지를 분석 수신 API로 보내고 데이터를 추출 합니다.

![샘플 영수증](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>수신 서비스에서 수행 하는 작업은 무엇 인가요? 

미리 작성 된 수신 확인 서비스는 &mdash; 식당, 소매점 또는 식료품 스토어에서 일반적으로 받게 되는 수신의 유형으로 판매 영수증의 콘텐츠를 추출 합니다.

### <a name="fields-extracted"></a>추출 된 필드

|Name| Type | Description | 텍스트 | 값 (표준화 된 출력) |
|:-----|:----|:----|:----| :----|
| ReceiptType | 문자열 | 판매 확인 유형 | 항목별 |  |
| MerchantName | 문자열 | 영수증을 발급 하는 판매자의 이름 | Contoso |  |
| MerchantPhoneNumber | phoneNumber | 나열 된 전화 판매 수 | 987-654-3210 | + 19876543210 |
| MerchantAddress | 문자열 | 판매 된 판매 주소 | 123 Main St Redmond WA 98052 |  |
| TransactionDate | date | 영수증이 발급 된 날짜 | June 06, 2019 | 2019-06-26  |
| TransactionTime | time | 수신이 발행 된 시간 | 오후 4:49 | 16:49:00  |
| 합계 | number | 전체 트랜잭션 수신 총 트랜잭션 | $14.34 | 14.34 |
| 소계 | number | 수령의 부분합 (보통 세금이 적용 되기 전) | $12.34 | 12.34 |
| 세금 | number | 수령 on 수령, 자주 판매 세금 또는 동급 | $2.00 | 2.00 |
| 팁 | number | 구매자에 의해 포함 된 팁 | $1.00 | 1.00 |
| 항목 | 개체의 배열 | 추출 된 품목 (이름, 수량, 단가 및 총 가격) | |
| 이름 | string | 항목 이름 | Surface Pro 6 | |
| 수량 | number | 각 항목의 수량 | 1 | |
| 가격 | number | 각 항목 단위의 개별 가격 | $999.00 | 999.00 |
| 총 가격 | number | 품목의 총 가격 | $999.00 | 999.00 |

### <a name="additional-features"></a>추가 기능

또한 수신 API는 다음 정보를 반환 합니다.

* 필드 신뢰 수준 (각 필드는 연결 된 신뢰도 값을 반환)
* OCR 원시 텍스트 (전체 수신에 대 한 OCR 추출 된 텍스트 출력)
* 각 값, 줄 및 단어에 대 한 경계 상자

## <a name="try-it-out"></a>사용해 보기

양식 인식기 수신 서비스를 사용해 보려면 온라인 샘플 UI 도구로 이동 합니다.

> [!div class="nextstepaction"]
> [미리 빌드된 모델 사용해보기](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>지원 되는 로캘 

* GA ( **미리 작성 된 수신** )는 en-us 로캘의 판매 영수증을 지원 합니다.
* **미리 작성 된 수령 v 2.1-preview. 3** (공개 미리 보기)에는 다음 EN 수신 로캘에 대 한 추가 지원이 추가 되었습니다. 
  * EN-오스트레일리아 
  * EN-CA 
  * EN-US 
  * EN-US 

  > [!NOTE]
  > 언어 입력 
  >
  > 미리 작성 된 수신 v 2.1-preview. 3에는 추가 영어 시장에서 수신 로캘을 지정 하는 선택적 요청 매개 변수가 있습니다. 오스트레일리아 (EN-US), 캐나다 (EN-US), 뛰어난 Britain (en-us) 및 인도 (EN-US)의 영어로 판매 영수증을 위해 로캘을 지정 하 여 향상 된 결과를 얻을 수 있습니다. V 2.1-preview. 3에 지정 된 로캘이 없으면 모델은 기본적으로 EN-US 모델로 지정 됩니다.


## <a name="the-analyze-receipt-operation"></a>분석 확인 작업

[분석 확인](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync) 은 확인의 이미지나 PDF를 입력으로 사용 하 고 관심 및 텍스트의 값을 추출 합니다. 호출은 라는 응답 헤더 필드를 반환 합니다 `Operation-Location` . `Operation-Location`값은 다음 단계에서 사용할 결과 ID를 포함 하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>수신 결과 분석 가져오기 작업

두 번째 단계는 [분석 수신 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult) 작업을 호출 하는 것입니다. 이 작업은 확인 분석 작업으로 만들어진 결과 ID를 입력으로 사용 합니다. 이 메서드는 다음과 같은 가능한 값을 포함 하는 **상태** 필드를 포함 하는 JSON 응답을 반환 합니다. **성공** 값이 반환 될 때까지이 작업을 반복적으로 호출 합니다. 초당 요청 수 (RPS)를 초과 하지 않도록 3 ~ 5 초 간격을 사용 합니다.

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 작업이 시작 되지 않았습니다. |
| |  | 실행 중: 분석 작업이 진행 중입니다. |
| |  | 실패: 분석 작업이 실패 했습니다. |
| |  | 성공: 분석 작업이 성공 했습니다. |

**상태** 필드의 **성공** 값이 이면 JSON 응답에는 수신 이해 및 텍스트 인식 결과가 포함 됩니다. 수신 확인 결과는 명명 된 필드 값의 사전으로 구성 됩니다. 각 값에는 추출 된 텍스트, 정규화 된 값, 경계 상자, 신뢰도 및 해당 word 요소가 포함 됩니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보를 사용 하 여 선과 단어의 계층 구조로 구성 됩니다.

![샘플 수신 결과](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>샘플 JSON 출력


수신 분석 수신 결과 가져오기 작업에 대 한 응답은 모든 정보가 추출 된 수신 확인을 구조화 된 방식으로 표현한 것입니다.  [샘플 수신 파일](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg) 및 해당 구조화 된 출력 [샘플 수신 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)은 여기를 참조 하세요.

성공적인 JSON 응답의 다음 예를 참조 하세요.
* `"readResults"` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. 
* `"documentResults"` 노드에는 모델이 검색한 명함 특정 값이 포함됩니다. 여기에서 이름, 성, 회사 이름 등의 유용한 키/값 쌍을 찾을 수 있습니다.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="customer-scenarios"></a>고객 시나리오  

수신 API를 사용 하 여 추출 된 데이터를 사용 하 여 다양 한 작업을 수행할 수 있습니다. 다음은 고객이 수신 API를 사용 하 여 수행 하는 작업의 몇 가지 예입니다.

### <a name="business-expense-reporting"></a>비즈니스 비용 보고  

비즈니스 경비를 확인 하는 것은 종종 수신 이미지에서 데이터를 수동으로 입력 하는 시간을 지출 합니다. 수신 API를 사용 하면 추출 된 필드를 사용 하 여이 프로세스를 부분적으로 자동화 하 고 확인을 신속 하 게 분석할 수 있습니다.  

수신 API는 추출 된 필드 값을 여러 가지 방법으로 사용할 수 있도록 하는 간단한 JSON 출력입니다. 내부 비용 응용 프로그램과 통합 하 여 경비 보고서를 미리 채웁니다. 이 시나리오에 대 한 자세한 내용은 Acumatica에서 수신 API를 활용 하 여 [비용을 보고](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure)하는 프로세스를 확인 하는 방법을 참조 하세요.  

### <a name="auditing-and-accounting"></a>감사 및 계정

수신 API 출력은 expense reporting 및 보상 프로세스의 다양 한 지점에서 많은 비용을 분석 하는 데 사용할 수도 있습니다. 수신 확인을 처리 하 여 수동 감사 또는 빠른 승인에 대해 심사를 수행할 수 있습니다.  

수신 출력은 업무용 또는 개인용 사용을 위한 일반 책에도 유용 합니다. 수신 API를 사용 하 여 원시 수신 이미지/PDF 데이터를 실행 가능한 디지털 출력으로 변환 합니다.

### <a name="consumer-behavior"></a>소비자 동작 

수신 확인에는 소비자 동작 및 쇼핑 추세를 분석 하는 데 사용할 수 있는 유용한 데이터가 포함 되어 있습니다.

또한 수신 API는 [AI 작성기 수신 처리 기능](/ai-builder/prebuilt-receipt-processing)을 향상 시킵니다.

## <a name="next-steps"></a>다음 단계

 . 선택한 개발 언어로 폼 인식기를 사용 하 여 수신 처리 앱을 작성 하기 시작 합니다.

> [!div class="nextstepaction"]
> [양식 인식기 빠른 시작 완료](quickstarts/client-library.md)

## <a name="see-also"></a>참조

* [Form Recognizer란?](overview.md)
* [양식 인식기 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)
>
