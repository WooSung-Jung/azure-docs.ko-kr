---
title: 워크플로의 오류 및 예외 처리
description: Azure Logic Apps를 사용 하 여 만든 자동화 된 작업 및 워크플로에서 발생 하는 오류 및 예외를 처리 하는 방법에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: estfan, logicappspm, azla
ms.date: 02/18/2021
ms.topic: article
ms.openlocfilehash: fbe797937021763bb97ca09e1da792d9a7010f9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702507"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Azure Logic Apps에서 예외 및 오류 처리

통합 아키텍처가 가동 중지 시간 또는 종속 시스템에 의해 발생한 문제를 적절하게 처리하는 일은 어려울 수 있습니다. Logic Apps는 문제와 실패에서 정상적으로 처리하는 강력하고 복원 가능한 통합을 구현하기 위해 오류와 예외를 처리하는 최상의 환경을 제공합니다.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>재시도 정책

가장 기본적인 예외 및 오류 처리를 위해 지원 되는 모든 작업 또는 트리거에서 *다시 시도 정책을* 사용할 수 있습니다 (예: [HTTP 동작](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)참조). 재시도 정책은 원래 요청(408, 429 또는 5xx 응답을 나타내는 모든 요청) 이 시간 초과되거나 실패할 때 작업 또는 트리거가 요청을 다시 시도하는지 여부 및 그 방법을 지정합니다. 사용되는 다른 재시도 정책이 없으면 기본 정책이 사용됩니다.

재시도 정책 유형은 다음과 같습니다.

| Type | Description |
|------|-------------|
| **기본값** | 이 정책은 7.5초마다 *기하급수적으로 증가하는* 간격으로 최대 4번의 다시 시도를 보냅니다. 7.5초마다 증가하지만 5 ~ 45초 사이로 제한됩니다. |
| **기하급수적 간격**  | 이 정책은 다음 요청을 보내기 전에 기하급수적으로 증가하는 범위에서 선택된 임의의 간격만큼 대기합니다. |
| **고정 간격**  | 이 정책은 다음 요청을 보내기 전에 지정된 간격만큼 대기합니다. |
| **없음**  | 요청을 다시 보내지 않습니다. |
|||

재시도 정책 제한에 대한 자세한 내용은 [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#http-limits)을 참조하세요.

### <a name="change-retry-policy"></a>재시도 정책 변경

다른 재시도 정책을 선택하려면 다음 단계를 따릅니다.

1. Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 작업 또는 트리거에 대한 **설정** 을 엽니다.

1. 작업 또는 트리거가 재시도 정책을 지원하는 경우 **재시도 정책** 에서 원하는 유형을 선택합니다.

또는 재시도 정책을 지원하는 작업 또는 트리거에 대한 `inputs` 섹션에서 재시도 정책을 수동으로 지정할 수 있습니다. 재시도 정책을 지정하지 않으면 작업에 기본 정책이 사용됩니다.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 |
|-------|------|-------------|
| <*다시 시도 정책-유형*> | String | 사용할 재시도 정책 유형(`default`, `none`, `fixed` 또는 `exponential`) |
| <*다시 시도 간격*> | String | 해당 값이 [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 사용해야 하는 재시도 간격입니다. 기본 최소 간격은 `PT5S`이고 최대 간격은 `PT1D`입니다. 지수 간격 정책을 사용하면 다른 최소값 및 최대값을 지정할 수 있습니다. |
| <*다시 시도 횟수*> | 정수 | 재시도 횟수이며, 1~90 사이여야 합니다. |
||||

*선택*

| 값 | Type | 설명 |
|-------|------|-------------|
| <*최소 간격*> | String | 지수 간격 정책에서 임의로 선택한 간격의 최소 간격([ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)) |
| <*최대 간격*> | String | 지수 간격 정책에서 임의로 선택한 간격의 최대 간격([ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)) |
||||

다른 정책 유형에 대한 자세한 내용은 다음과 같습니다.

<a name="default-retry"></a>

### <a name="default"></a>기본값

재시도 정책을 정의하지 않으면, 실제로 작업에서는 기하급수적으로 증가하는 간격(7.5초마다 증가)으로 최대 네 번의 재시도를 보내는 [지수 간격 정책](#exponential-interval)인 기본 정책을 사용합니다. 간격은 5~45초 사이로 제한됩니다.

작업 또는 트리거에 명시적으로 정의되지 않지만 기본 정책이 예제 HTTP 작업에서 동작하는 방식은 다음과 같습니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>없음

작업 또는 트리거가 실패한 요청을 재시도하지 않도록 지정하려면 <*retry-policy-type*>을 `none`으로 설정합니다.

### <a name="fixed-interval"></a>고정 간격

작업 또는 트리거가 다음 요청을 보내기 전에 지정된 간격 동안 대기하도록 지정하려면 <*retry-policy-type*>을 `fixed`로 설정합니다.

*예제*

이 재시도 정책은 각 시도 간에 30초를 지연하면서 실패한 첫 번째 요청 후에 최신 뉴스를 두 번 더 가져오려고 합니다.

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>기하급수적 간격

작업 또는 트리거가 다음 요청을 보내기 전에 임의 간격 동안 대기하도록 지정하려면 <*retry-policy-type*>을 `exponential`로 설정합니다. 임의 간격은 기하급수적으로 증가하는 범위에서 선택됩니다. 필요에 따라, 고유한 최소 및 최대 간격을 지정하여 기본 최소 및 최대 간격을 재정의할 수도 있습니다.

**임의 변화 범위**

다음 표에서는 Logic Apps가 지정된 재시도 횟수까지 각 재시도에 대해 지정된 범위에서 균일한 임의 변화를 생성하는 방법을 보여 줍니다.

| 재시도 횟수 | 최소 간격 | 최대 간격 |
|--------------|------------------|------------------|
| 1 | max (0, <*최소 간격*>) | min (interval, <*최대 간격*>) |
| 2 | max (interval, <*최소 간격*>) | min (2 * interval, <*최대 간격*>) |
| 3 | 최대 (2 * 간격, <*최소 간격*>) | min (4 * interval, <*최대 간격*>) |
| 4 | 최대 (4 * 간격, <*최소 간격*>) | min (8 * interval, <*최대 간격*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>"다음 이후 실행" 동작을 변경 하 여 오류를 Catch 하 고 처리 합니다.

논리 앱 디자이너에서 작업을 추가 하는 경우 이러한 작업을 실행 하는 데 사용할 순서를 암시적으로 선언 합니다. 작업 실행이 완료 되 면 해당 작업은,, 또는와 같은 상태로 표시 됩니다 `Succeeded` `Failed` `Skipped` `TimedOut` . 각 작업 정의에서 속성은 `runAfter` 먼저 완료 해야 하는 선행 작업과 후속 작업을 실행 하기 전에 해당 선행 작업에 대해 허용 된 상태를 지정 합니다. 기본적으로 디자이너에서 추가 하는 동작은 선행 작업이 상태로 완료 된 후에만 실행 됩니다 `Succeeded` .

동작에서 처리 되지 않은 오류 또는 예외가 throw 되 면 작업이 표시 되 `Failed` 고 모든 후속 작업이 표시 됩니다 `Skipped` . 병렬 분기가 있는 작업에 대해이 동작을 수행 하는 경우 Logic Apps 엔진은 다른 분기를 따라 완료 상태를 확인 합니다. 예를 들어 분기가 작업으로 끝나는 경우 `Skipped` 해당 분기의 완료 상태는 건너뛴 작업의 선행 작업 상태를 기준으로 합니다. 논리 앱 실행이 완료 되 면 엔진은 모든 분기 상태를 평가 하 여 전체 실행의 상태를 확인 합니다. 오류가 발생 하는 분기가 있으면 전체 논리 앱 실행이 표시 됩니다 `Failed` .

![실행 상태를 평가 하는 방법을 보여 주는 예제](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

선행 작업의 상태에도 불구 하 고 작업을 계속 실행할 수 있도록 하려면 작업 [의 "실행 후" 동작을 사용자 지정](#customize-run-after) 하 여 선행 작업의 실패 상태를 처리 합니다.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>"실행 후" 동작 사용자 지정

선행 작업의 상태가 `Succeeded` , `Failed` , `Skipped` , `TimedOut` 또는 이러한 상태 중 하나일 때 동작이 실행 되도록 작업의 "실행 후" 동작을 사용자 지정할 수 있습니다. 예를 들어 Excel Online 선행 작업을로 표시 한 후 전자 메일을 보내려면 `Add_a_row_into_a_table` `Failed` 다음 단계를 수행 `Succeeded` 하 여 "다음 시간 이후에 실행" 동작을 변경 합니다.

* 디자인 뷰에서 줄임표 (**...**) 단추를 선택한 다음 다음 **이후 실행 구성** 을 선택 합니다.

  ![작업에 대해 "실행 후" 동작 구성](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  Action 셰이프는 선행 작업에 필요한 기본 상태를 표시 합니다 .이 작업은 다음 예제에서 **테이블에 행을 추가** 합니다.

  ![동작에 대 한 기본 "실행 후" 동작](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  이 예에서 **실패** 한 상태로 "실행 후" 동작을 다음과 같이 변경 합니다.

  !["실행 후" 동작을 "실패 함"으로 변경](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  선행 작업을로 표시할지 여부를 지정 하려면 작업을 실행 하 `Failed` 고 `Skipped` `TimedOut` 다른 상태를 선택 합니다.

  ![다른 상태를 갖도록 "실행 후" 동작 변경](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* 코드 보기의 작업 JSON 정의에서 `runAfter` 다음 구문을 따르는 속성을 편집 합니다.

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  이 예에서는 `runAfter` 속성을에서로 변경 `Succeeded` 합니다 `Failed` .

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  선행 작업을로 표시할지 여부를 지정 하려면 작업을 실행 하 `Failed` 고 `Skipped` `TimedOut` 다른 상태를 추가 합니다.

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>범위 및 해당 결과를 사용하여 작업 평가

속성을 사용한 개별 작업 후 단계를 실행 하는 것과 마찬가지로 `runAfter` [범위](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)내에서 작업을 함께 그룹화 할 수 있습니다. 작업을 논리적으로 그룹화하고, 범위의 집계 상태를 평가하고, 해당 상태에 따라 작업을 수행하려는 경우 범위를 사용할 수 있습니다. 범위 내 모든 작업의 실행이 완료되면 범위 자체에서 자체의 상태를 가져옵니다.

범위의 상태를 확인 하려면, 등의 논리 앱 실행 상태를 확인 하는 데 사용 하는 것과 동일한 기준을 사용할 수 있습니다 `Succeeded` `Failed` .

기본적으로 범위의 모든 작업이 성공 하면 범위 상태가로 표시 됩니다 `Succeeded` . 범위에 있는 최종 동작이 `Failed` 또는 이면 `Aborted` 범위 상태가로 표시 됩니다 `Failed` .

범위에서 예외를 catch 하 `Failed` 고 해당 오류를 처리 하는 작업을 실행 하려면 `runAfter` 해당 범위에 대해 속성을 사용할 수 있습니다 `Failed` . 이렇게 하면 범위의 작업이  실패 하 고 `runAfter` 해당 범위에 대 한 속성을 사용 하는 경우 오류를 catch 하는 단일 작업을 만들 수 있습니다.

범위에 대한 제한은 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요.

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>실패에 대한 컨텍스트 및 결과 가져오기

범위에서 실패를 catch하는 것이 유용하지만, 실패한 작업과 반환된 오류 또는 상태 코드를 정확히 파악하는 데 도움이 되는 컨텍스트가 필요할 수 있습니다. [ `result()` 함수](../logic-apps/workflow-definition-language-functions-reference.md#result) 는 범위 이름에 해당 하는 단일 매개 변수를 수락 하 고 첫 번째 수준 작업의 결과를 포함 하는 배열을 반환 하 여 범위 지정 작업의 최상위 작업에서 결과를 반환 합니다. 이러한 작업 개체에는 `actions()` 작업 시작 시간, 종료 시간, 상태, 입력, 상관 관계 id 및 출력과 같이 함수에서 반환 된 특성과 동일한 특성이 포함 됩니다. 

> [!NOTE]
> `result()`함수는 switch 또는 condition 작업과 같은 보다 심층적인 중첩 된 작업이 아니라 첫 번째 수준 작업 *에서만* 결과를 반환 합니다.

범위에서 실패 한 작업에 대 한 컨텍스트를 가져오려면 `@result()` 범위의 이름 및 속성으로 식을 사용할 수 있습니다 `runAfter` . 반환 된 배열을 상태를 포함 하는 작업으로 필터링 하려면 `Failed` [ **배열 필터** 작업](logic-apps-perform-data-operations.md#filter-array-action)을 추가 하면 됩니다. 반환 된 실패 작업에 대 한 작업을 실행 하려면 반환 된 필터링 된 배열을 사용 하 고 [ **for each** 루프](../logic-apps/logic-apps-control-flow-loops.md)를 사용 합니다.

다음 예제에는 "My_Scope" 이라는 범위 동작 내에서 실패 한 작업에 대 한 응답 본문과 함께 HTTP POST 요청을 전송 하는 자세한 설명이 나와 있습니다.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

이 예제에서 수행되는 작업을 설명하는 자세한 연습은 다음과 같습니다.

1. “My_Scope” 내의 모든 작업에서 결과를 가져오기 위해 **배열 필터링** 작업은 필터 식 `@result('My_Scope')`를 사용합니다.

1. **필터 배열의** 조건은 `@result()` 상태가와 같은 모든 항목입니다 `Failed` . 이 조건은 “My_Scope”의 모든 작업 결과에 대한 배열을 실패한 작업 결과만 있는 배열로 필터링합니다.

1. `For_each` *필터링 된 배열* 출력에 대해 루프 동작을 수행 합니다. 이 단계는 이전에 필터링된 실패한 작업 결과 각각에 대해 작업을 수행합니다.

   범위에서 단일 작업이 실패 하는 경우 루프의 작업은 `For_each` 한 번만 실행 됩니다. 여러 실패한 작업에서 오류당 하나의 작업이 발생합니다.

1. 항목인 응답 본문에 HTTP POST `For_each` 를 보냅니다 `@item()['outputs']['body']` .

   `@result()` 항목 모양은 `@actions()` 모양과 같으며 동일한 방식으로 구문 분석할 수 있습니다.

1. 실패한 작업 이름(`@item()['name']`) 및 실패한 실행 클라이언트 추적 ID(`@item()['clientTrackingId']`)가 있는 두 개의 사용자 지정 헤더를 포함됩니다.

참고로 이전 예제에서 구문 분석하는 `name`, `body` 및 `clientTrackingId` 속성을 보여 주는 단일 `@result()` 항목의 예제는 다음과 같습니다. 동작 외부 `For_each` 에서는 `@result()` 이러한 개체의 배열을 반환 합니다.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

다른 예외 처리 패턴을 수행하려면 이 문서의 앞부분에서 설명한 식을 사용할 수 있습니다. 필터링 된 전체 오류 배열을 허용 하는 범위 외부에서 단일 예외 처리 작업을 실행 하 고 작업을 제거 하도록 선택할 수 있습니다 `For_each` . 앞에서 설명한 대로 응답에서 기타 유용한 속성을 포함할 수도 있습니다 `\@result()` .

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor 로그 설정

이전 패턴은 실행 내에서 오류 및 예외를 처리하는 훌륭한 방법이지만 실행 자체와는 독립적으로 오류를 식별하고 오류에 대응할 수도 있습니다. [Azure Monitor](../azure-monitor/overview.md) 은 모든 실행 및 작업 상태를 포함 하 여 모든 워크플로 이벤트를 [Log Analytics 작업 영역](../azure-monitor/logs/data-platform-logs.md), [azure storage 계정](../storage/blobs/storage-blobs-overview.md)또는 [azure Event Hubs](../event-hubs/event-hubs-about.md)에 전송 하는 간단한 방법을 제공 합니다.

실행 상태를 평가하려면 로그 및 메트릭을 모니터링하거나 선호하는 모든 모니터링 도구에 게시할 수 있습니다. 한 가지 잠재적 옵션은 Event Hubs를 통해 모든 이벤트를 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)로 스트리밍하는 것입니다. Stream Analytics에서는 진단 로그의 모든 잘못된 부분, 평균 또는 오류를 기반으로 라이브 쿼리를 작성할 수 있습니다. Stream Analytics를 사용하여 큐, 토픽, SQL, Azure Cosmos DB 및 Power BI와 같은 다른 데이터 원본에 정보를 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [고객이 Azure Logic Apps의 오류 처리를 빌드하는 방법을 참조하세요.](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Logic Apps 예제 및 시나리오 더 찾아보기](../logic-apps/logic-apps-examples-and-scenarios.md)
