---
title: 국가/지역별 Azure CDN 콘텐츠 제한
description: 지역 필터링 기능을 사용 하 여 Azure CDN 콘텐츠에 대 한 국가/지역별 액세스를 제한 하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 01/16/2021
ms.author: allensu
ms.openlocfilehash: 8901dffb752409acd7fb08a2025bed9a4cc70132
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539496"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>국가/지역별 Azure CDN 콘텐츠 제한

## <a name="overview"></a>개요
사용자가 콘텐츠를 요청 하면 모든 위치의 사용자에 게 콘텐츠가 제공 됩니다. 국가/지역별 콘텐츠에 대 한 액세스를 제한 하는 것이 좋습니다. 

*지역 필터링* 기능을 사용 하 여 CDN 끝점의 특정 경로에 대 한 규칙을 만들 수 있습니다. 선택한 국가/지역에서 콘텐츠를 허용 하거나 차단 하도록 규칙을 설정할 수 있습니다.

> [!IMPORTANT]
> **Microsoft의 Azure CDN 표준** 프로필은 경로 기반 지역 필터링을 지원하지 않습니다.
> 

## <a name="standard-profiles"></a>표준 프로필

이러한 지침은 Akamai의 **Azure CDN 표준과** **Verizon의 Azure CDN standard** 프로필에 대 한 것입니다.

**Verizon의 Azure CDN Premium** 프로필의 경우 **관리** 포털을 사용하여 지역 필터링을 활성화해야 합니다. 자세한 내용은 [Verizon의 Azure CDN Premium 프로필](#azure-cdn-premium-from-verizon-profiles)을 참조하세요.

### <a name="define-the-directory-path"></a>디렉터리 경로 정의
지역 필터링 기능에 액세스하려면 포털 내에서 CDN 엔드포인트를 선택한 다음, 왼쪽 메뉴의 설정 아래에서 **지역 필터링** 을 선택합니다. 

![끝점의 메뉴에서 선택한 지역 필터링을 보여 주는 스크린샷](./media/cdn-filtering/cdn-geo-filtering-standard.png)

**경로** 상자에서 사용자에게 액세스를 허용하거나 거부하는 위치에 대한 상대 경로를 지정합니다. 

슬래시(/)를 사용하여 모든 파일에 대해 지역 필터링을 적용하거나 디렉터리 경로(예: */pictures/*)를 지정하여 특정 폴더를 선택할 수 있습니다. 단일 파일(예: */pictures/city.png*)에 지역 필터링을 적용할 수도 있습니다. 여러 규칙을 사용할 수 있습니다. 규칙을 입력 하면 다음 규칙을 입력할 수 있는 빈 행이 나타납니다.

예를 들어 다음 모든 디렉터리 경로 필터가 유효합니다.   
*/*                                 
*사진의*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>작업의 유형 정의

**작업** 목록에서 **허용** 또는 **차단** 을 선택합니다. 

- **허용**: 지정 된 국가/지역의 사용자만 재귀 경로에서 요청 된 자산에 액세스할 수 있습니다.

- **차단**: 지정 된 국가/지역의 사용자는 재귀 경로에서 요청 된 자산에 대 한 액세스를 거부 합니다. 해당 위치에 대해 다른 국가/지역 필터링 옵션이 구성 되지 않은 경우 다른 모든 사용자에 게 액세스가 허용 됩니다.

예를 들어 */Photos/Strasbourg/* 경로 차단에 대한 지역 필터링 규칙은 다음 파일을 필터링합니다.     
*http: \/ / \<endpoint> . azureedge.net/Photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> . azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>국가/지역 정의

**국가 코드** 목록에서 차단 하거나 경로에 대해 허용 하려는 국가/지역을 선택 합니다. 

국가/지역 선택을 완료 한 후에는 **저장** 을 선택 하 여 새 지역 필터링 규칙을 활성화 합니다. 

![국가 또는 지역을 차단 하거나 허용 하는 데 사용할 국가 코드를 보여 주는 스크린샷](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>리소스 정리

규칙을 삭제하려면 **지역 필터링** 페이지의 목록에서 선택한 다음, **삭제** 를 선택합니다.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Verizon의 Azure CDN Premium 프로필

**Verizon의 Azure CDN 프리미엄** 프로필의 경우 지역 필터링 규칙을 만들기 위한 사용자 인터페이스는 다릅니다.

1. Azure CDN 프로필의 위쪽 메뉴에서 **관리** 를 선택합니다.

2. Verizon 포털에서 **HTTP Large** 를 선택한 다음, **국가 필터링** 을 선택합니다.

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="에서 국가 필터링을 선택 하는 방법을 보여 주는 스크린샷 Azure CDN" border="true":::
  
3. **국가 필터링 추가** 를 선택합니다.

4. **1 단계:** 에서 디렉터리 경로를 입력 합니다. **차단** 또는 **추가** 를 선택 하 고 **다음** 을 선택 합니다.

    > [!IMPORTANT]
    > 끝점 이름은 경로에 있어야 합니다.  예: **/myendpoint8675/myfolder**.  **Myendpoint8675** 을 사용자의 끝점 이름으로 바꿉니다.
    > 
    
5. **2 단계** 에서는 목록에서 국가/지역을 하나 이상 선택 합니다. **마침** 을 선택 하 여 규칙을 활성화 합니다. 
    
    **국가 필터링** 페이지의 테이블에 새 규칙이 표시됩니다.
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="국가 필터링에서 규칙이 표시 되는 위치를 보여 주는 스크린샷" border="true":::
 
### <a name="clean-up-resources"></a>리소스 정리
국가/지역 필터링 규칙 테이블에서 삭제할 규칙 옆에 있는 삭제 아이콘을 선택 하거나 편집 아이콘을 선택 하 여 수정 합니다.

## <a name="considerations"></a>고려 사항
* 지역 필터링 구성에 대 한 변경 내용은 즉시 적용 되지 않습니다.
   * **Microsoft의 Azure CDN 표준** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
   * **Akamai의 Azure CDN Standard** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. 
   * **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
 
* 이 기능은 와일드 카드 문자 (예: *)를 지원 하지 않습니다.

* 상대 경로와 관련된 지역 필터링 구성은 해당 경로에 재귀적으로 적용됩니다.

* 동일한 상대 경로에는 한 가지 규칙만 적용할 수 있습니다. 즉, 동일한 상대 경로를 가리키는 여러 국가/지역 필터를 만들 수 없습니다. 그러나 국가/지역 필터가 재귀적 이므로 폴더에는 국가/지역 필터가 여러 개 있을 수 있습니다. 즉, 이전에 구성 된 폴더의 하위 폴더에 다른 국가/지역 필터를 할당할 수 있습니다.

* 지역 필터링 기능은 국가 코드를 사용 하 여 보안 디렉터리에 대해 요청이 허용 또는 차단 되는 국가/지역을 정의 합니다. Akamai 및 Verizon 프로필은 동일한 국가 코드의 대부분을 지원하지만 몇 가지 차이점이 있습니다. 자세한 내용은 [Azure CDN 국가 코드](/previous-versions/azure/mt761717(v=azure.100))를 참조 하세요. 

