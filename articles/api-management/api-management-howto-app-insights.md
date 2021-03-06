---
title: Azure API Management Azure 애플리케이션 Insights와 통합
titleSuffix: Azure API Management
description: Azure Application Insights에서 Azure API Management의 이벤트를 기록하고 보는 방법을 알아봅니다.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564267"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure Application Insights와 Azure API Management를 통합하는 방법

Azure API Management는 Azure Application Insights와 쉽게 통합할 수 있습니다. Azure Application Insights는 웹 개발자가 여러 플랫폼에서 애플리케이션을 구축하고 관리할 수 있는 확장 가능한 서비스입니다. 이 가이드에서는 이러한 통합의 모든 단계를 안내하고, API Management 서비스 인스턴스의 성능에 미치는 영향을 줄이기 위한 전략에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 가이드를 수행하려면 Azure API Management 인스턴스가 있어야 합니다. 아직 없으면 먼저 [자습서](get-started-create-service-instance.md)를 완료하세요.

## <a name="create-an-application-insights-instance"></a>Application Insights 인스턴스 만들기

Application Insights를 사용 하려면 먼저 서비스의 인스턴스를 만들어야 합니다. Azure Portal를 사용 하 여 인스턴스를 만드는 단계는 [작업 영역 기반 Application Insights 리소스](../azure-monitor/app/create-workspace-resource.md)를 참조 하세요.
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Application Insights와 API Management 간의 연결 만들기

1. **Azure Portal** 에서 **Azure API Management 서비스 인스턴스로** 이동 합니다.
1. 왼쪽 메뉴에서 **Application Insights** 를 선택합니다.
1. **+ 추가** 를 클릭합니다.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="새 연결을 추가할 위치를 보여 주는 스크린샷":::
1. 앞에서 만든 **Application Insights** 인스턴스를 선택하고 간단한 설명을 제공합니다.
1. **만들기** 를 클릭합니다.
1. 이제 계측 키를 사용 하 여 Application Insights로 거를 만들었습니다. 이제 목록에 표시됩니다.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="계측 키를 사용 하 여 새로 만든 Application Insights로 거를 볼 수 있는 위치를 보여 주는 스크린샷":::

> [!NOTE]
> 내부적으로 Application Insights 인스턴스의 계측 키를 포함하는 API Management 인스턴스에 [로거](/rest/api/apimanagement/2019-12-01/logger/createorupdate) 엔터티가 만들어집니다.

## <a name="enable-application-insights-logging-for-your-api"></a>API에 대한 Application Insights 로깅 사용

1. **Azure Portal** 에서 **Azure API Management 서비스 인스턴스로** 이동 합니다.
1. 왼쪽 메뉴에서 **API** 를 선택합니다.
1. API를 클릭합니다(여기서는 **데모 회의 API**). 구성 된 경우 버전을 선택 합니다.
1. 위쪽 표시줄의 **설정** 탭으로 이동합니다.
1. **진단 로그** 섹션까지 아래로 스크롤합니다.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Application Insights 로거":::
1. **사용** 확인란을 선택합니다.
1. **대상** 드롭다운에서 연결된 로거를 선택합니다.
1. **100** 을 **샘플링 (%)** 으로 입력 하 고 **항상 오류 로그** 확인란을 선택 합니다.
1. **저장** 을 선택합니다.

> [!WARNING]
> **페이로드 바이트 수로** 기본값 **0** 을 재정의 하면 api의 성능이 상당히 저하 될 수 있습니다.

> [!NOTE]
> 내부적으로 이름이 'applicationinsights'인 [진단](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) 엔터티가 API 수준에서 만들어집니다.

| 설정 이름                        | 값 형식                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 사용                              | boolean                           | 이 API의 로깅이 사용되는지 여부를 지정합니다.                                                                                                                                                                                                                                                                                                |
| 대상                         | Azure Application Insights 로거 | 사용할 Azure Application Insights 로거를 지정합니다.                                                                                                                                                                                                                                                                                           |
| 샘플링(%)                        | decimal                           | 0-100의 값(퍼센트)입니다. <br/> Application Insights에 기록 될 요청 비율을 지정 합니다. 0% 샘플링은 기록되는 요청이 없음을 의미하고, 100%는 모든 요청이 기록됨을 의미합니다. <br/> Application Insights에 대 한 요청을 로깅할 때 성능에 대 한 영향을 줄이려면이 설정을 사용 합니다. [성능 영향 및 로그 샘플링을](#performance-implications-and-log-sampling)참조 하세요. |
| 항상 오류 로깅                   | boolean                           | 이 설정을 선택 하면 **샘플링** 설정에 관계 없이 모든 오류가 Application Insights에 기록 됩니다.   
| 로그 클라이언트 IP 주소 | |  이 설정을 선택 하면 API 요청에 대 한 클라이언트 IP 주소가 Application Insights에 기록 됩니다.                                         |
| 자세한 정도         |                                   | 자세한 정도를 지정 합니다. 심각도가 높은 사용자 지정 추적만 로깅됩니다. 기본값: 정보      | 
| 상관 관계 프로토콜 |  |  여러 구성 요소에서 보낸 원격 분석을 상호 연결 하는 데 사용할 프로토콜을 선택 합니다. 기본값: 레거시 <br/>자세한 내용은 [Application Insights 원격 분석 상관 관계](../azure-monitor/app/correlation.md)를 참조 하세요.  |
| 기본 옵션: 로깅할 헤더              | list                              | 요청 및 응답에 대 한 Application Insights에 기록 될 헤더를 지정 합니다.  기본값: 헤더가 기록되지 않음                                                                                                                                                                                                             |
| 기본 옵션: 로깅할 페이로드 바이트 수 | 정수                           | 요청 및 응답에 대 한 Application Insights에 기록 되는 본문의 첫 번째 바이트 수를 지정 합니다.  기본값: 0.                                                                                                                                                                                                    |
| 고급 옵션: 프런트 엔드 요청  |                                   | *프런트 엔드 요청이* Application Insights에 기록 되는지 여부와 방법을 지정 합니다. *프런트 엔드 요청* 은 Azure API Management 서비스로 들어오는 요청입니다.                                                                                                                                                                        |
| 고급 옵션: 프런트 엔드 응답 |                                   | *프런트 엔드 응답* 을 Application Insights에 기록할지 여부와 방법을 지정 합니다. *프런트 엔드 응답* 은 Azure API Management 서비스에서 나가는 응답입니다.                                                                                                                                                                   |
| 고급 옵션: 백 엔드 요청   |                                   | *백 엔드 요청이* Application Insights에 기록 되는지 여부와 방법을 지정 합니다. *백 엔드 요청* 은 Azure API Management 서비스에서 나가는 요청입니다.                                                                                                                                                                        |
| 고급 옵션: 백 엔드 응답  |                                   | Application Insights에 *백 엔드 응답* 을 기록할지 여부와 방법을 지정 합니다. *백 엔드 응답* 은 Azure API Management 서비스로 들어오는 응답입니다.                                                                                                                                                                       |

> [!NOTE]
> 로거(단일 API 로거 또는 모든 API에 대한 로거)는 서로 다른 수준에서 지정할 수 있습니다.
>  
> 둘 다 지정하는 경우:
> + 서로 다른 로거이면 둘 다 사용됩니다(로그 멀티플렉싱).
> + 동일한 로거이지만 서로 다른 설정을 사용하면 단일 API(세분화된 수준)에 대한 로거가 모든 API에 대한 로거를 재정의합니다.

## <a name="what-data-is-added-to-application-insights"></a>Application Insights에 추가 되는 데이터

Application Insights 수신:

+ 들어오는 모든 요청(*프런트 엔드 요청*, *프런트 엔드 응답*)에 대한 *요청* 원격 분석 항목
+ 백 엔드 서비스(*백 엔드 요청*, *백 엔드 응답*)에 전달되는 모든 요청에 대한 *종속성* 원격 분석 항목
+ 실패 한 모든 요청에 대 한 *예외* 원격 분석 항목:
    + 클라이언트 연결이 닫 혔 기 때문에 실패 했습니다.
    + API 정책의 *오류에 대* 한 섹션을 트리거 했습니다.
    + 4xx 또는 5xx와 일치하는 응답 HTTP 상태 코드가 있는 요청
+ [추적 정책을 구성](api-management-advanced-policies.md#Trace) 하는 경우 원격 분석 항목을 *추적* 합니다. `severity` `trace` 정책의 설정은 Application Insights 로깅 설정 보다 크거나 같아야 합니다 `verbosity` .

> [!NOTE]
> Application Insights 인스턴스당 메트릭 및 이벤트의 최대 크기 및 수에 대 한 자세한 내용은 [Application Insights 제한](../azure-monitor/service-limits.md#application-insights) 을 참조 하세요.

## <a name="performance-implications-and-log-sampling"></a>성능에 미치는 영향 및 로그 샘플링

> [!WARNING]
> 모든 이벤트를 기록하면 들어오는 요청 속도에 따라 성능에 심각한 영향을 미칠 수 있습니다.

내부 부하 테스트에 따라 이 기능을 사용하면 요청 속도가 초당 1,000개 요청을 초과할 때 처리량이 40-50% 감소합니다. Application Insights는 응용 프로그램 성능 평가를 위해 통계 분석을 사용 하도록 설계 되었습니다. 감사 시스템이 아니며, 대용량 API에 대한 개별 요청 각각을 기록하는 데 적합하지 않습니다.

**샘플링** 설정 (이전 단계 참조)을 조정 하 여 기록 되는 요청 수를 조작할 수 있습니다. 값이 100% 이면 모든 요청이 기록 되 고 0%에는 로깅이 반영 되지 않습니다. **샘플링** 을 사용 하면 로깅의 이점을 계속 활용 하면서 원격 분석의 양을 줄이고 상당한 성능 저하를 효과적으로 방지할 수 있습니다.

또한 요청의 헤더와 본문에 대한 로깅을 건너뛰면 성능 문제를 완화하는 데 긍정적인 영향을 줍니다.

## <a name="video"></a>비디오

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>다음 단계

+ [Azure Application Insights](/azure/application-insights/)에 대해 자세히 알아봅니다.
+ [Azure Event Hubs에 로깅](api-management-howto-log-event-hubs.md)을 살펴봅니다.
