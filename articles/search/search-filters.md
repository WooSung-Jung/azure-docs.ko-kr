---
title: 검색 결과에 대 한 필터링
titleSuffix: Azure Cognitive Search
description: 사용자 보안 id, 언어, 지리적 위치 또는 숫자 값을 기준으로 필터링 하 여 Microsoft Azure에서 호스팅되는 클라우드 search 서비스인 Azure Cognitive Search의 쿼리에 대 한 검색 결과를 줄입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ba538f4753c2365406bd88286b6d54cff1a9e9ea
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800825"
---
# <a name="filters-in-azure-cognitive-search"></a>Azure Cognitive Search의 필터 

*필터* 는 쿼리에 사용 되는 문서를 선택 하기 위한 값 기반 기준을 제공 합니다. 필터는 단일 값 또는 OData [필터 식일](search-query-odata-filter.md)수 있습니다. 전체 텍스트 검색과 달리 필터 값 또는 식만 엄격한 일치를 반환 합니다.

[패싯 탐색과](search-filters-facets.md)같은 일부 검색 환경은 구현 과정에서 필터에 따라 달라 지지만 쿼리 범위를 특정 값으로 지정할 때마다 필터를 사용할 수 있습니다. 대신 특정 필드에 대 한 쿼리의 범위를 지정 하는 것이 목표 인 경우 아래에 설명 된 대체 방법이 있습니다.

## <a name="when-to-use-a-filter"></a>필터를 사용하는 경우

필터는 "내 주변 찾기", 패싯 탐색, 사용자가 볼 수 있는 문서만 표시하는 보안 필터를 비롯한 여러 검색 환경의 기초입니다. 이러한 환경 중 하나를 구현하는 경우 필터가 필요합니다. 지리적 위치 좌표, 사용자가 선택한 패싯 범주 또는 요청자의 보안 ID를 제공하는 검색 쿼리에 연결된 필터입니다.

일반적인 시나리오는 다음과 같습니다.

+ 인덱스의 내용에 따라 검색 결과를 조각화 합니다. 호텔 위치, 범주 및 편의 시설를 포함 하는 스키마가 제공 되 면 조건 (시애틀, 물, 보기)에서 명시적으로 일치 하는 필터를 만들 수 있습니다. 

+ 검색 환경을 구현 하는 것은 필터 요구 사항과 함께 제공 됩니다.

  + [패싯 탐색](search-faceted-navigation.md)은 사용자가 선택한 패싯 범주를 다시 전달하는 데 필터를 사용합니다.
  + 지리적 검색은 "내 주변 찾기" 앱에서 현재 위치의 좌표를 전달하는 데 필터를 사용합니다. 
  + [보안 필터](search-security-trimming-for-azure-search.md) 는 보안 식별자를 필터 조건으로 전달 합니다. 여기서 인덱스의 일치 항목은 문서에 대 한 액세스 권한의 프록시로 사용 됩니다.

+ "숫자 검색"을 수행 합니다. 숫자 필드는 검색할 수 있으며 검색 결과에 나타날 수 있지만 개별적으로 검색할 수 없습니다 (전체 텍스트 검색에 적용). 숫자 데이터를 기반으로 하는 선택 조건이 필요한 경우 필터를 사용합니다.

### <a name="alternative-methods-for-reducing-scope"></a>범위를 줄이기 위한 대체 방법

검색 결과를 효과적으로 줄이고 싶을 때 필터 외에 다른 방법도 있습니다. 목적에 따라 이러한 대안이 더 나을 수 있습니다.

+ `searchFields` 쿼리 매개 변수는 특정 필드로 검색을 제한 합니다. 예를 들어, 인덱스에서 영어와 스페인어 설명을 위한 별도의 필드를 제공하는 경우 searchFields를 사용하여 전체 텍스트 검색에 사용할 대상 필드를 지정할 수 있습니다. 

+ `$select` 매개 변수는 결과 집합에 포함할 필드를 지정하는 데 사용되며 호출 애플리케이션에 보내기 전에 효과적으로 응답을 조정합니다. 이 매개 변수는 쿼리를 구체화 하거나 문서 컬렉션을 축소 하지는 않지만, 더 작은 응답이 목표 이면이 매개 변수를 고려해 야 합니다. 

두 매개 변수에 대한 자세한 내용은 [문서 검색 > 요청 > 쿼리 매개 변수](/rest/api/searchservice/search-documents#query-parameters)를 참조하세요.

## <a name="how-filters-are-executed"></a>필터 실행 방법

쿼리 시 필터 파서는 조건을 입력으로 허용 하 고, 식을 트리로 표시 되는 원자성 부울 식으로 변환한 다음, 인덱스의 필터링 가능한 필드에 대해 필터 트리를 계산 합니다.

필터링은 문서 검색 및 관련성 점수 매기기를 위해 다운스트림 처리에 포함할 문서를 정규화 하는 검색을 통해 수행 됩니다. 검색 문자열과 페어링된 경우 필터는 후속 검색 작업의 회수 집합을 효과적으로 줄입니다. 단독으로 사용되면(예를 들어 `search=*` 같이 쿼리 문자열이 비어 있는 경우) 필터 조건이 유일한 입력입니다. 

## <a name="defining-filters"></a>필터 정의

필터는 OData 식 이며 Cognitive Search에서 지 원하는 [필터 구문](search-query-odata-filter.md) 에서 더 이상입니다.

**검색** 작업 마다 하나의 필터를 지정할 수 있지만 필터 자체에는 여러 필드와 여러 조건이 포함 될 수 있으며, **ismatch** 함수를 사용 하는 경우 여러 전체 텍스트 검색 식이 포함 될 수 있습니다. 여러 부분으로 구성 된 필터 식에서 연산자 우선 순위 규칙에 따라 조건자를 순서에 관계 없이 지정할 수 있습니다. 특정한 순서로 조건자를 다시 정렬해도 성능에 별다른 도움은 되지 않습니다.

필터 식에 대 한 한도는 요청에 대 한 최대 크기 제한입니다. 필터를 포함한 전체 요청은 POST의 경우 최대 16MB, GET의 경우 최대 8KB가 될 수 있습니다. 필터 식의 절 수에도 제한이 있습니다. 경험에 따르면 수백 개의 절이 있는 경우 제한에 도달할 위험이 있습니다. 애플리케이션을 설계할 때 필터의 크기를 무제한으로 생성하지 않는 것이 좋습니다.

다음 예제는 여러 API의 정형화된 필터 정의를 나타냅니다.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>필터 사용 패턴

다음 예에서는 필터 시나리오에 대 한 몇 가지 사용 패턴을 보여 줍니다. 자세한 내용은 [OData 식 구문 > 예제](./search-query-odata-filter.md#examples)를 참조하세요.

+ 독립 실행형 **$filter** 는 쿼리 문자열 없이, 필터 식이 관심 있는 문서를 정규화할 수 있을 때 유용합니다. 쿼리 문자열이 없으면 어휘 또는 언어 분석, 점수 매기기 및 순위 지정 등이 없으며 검색 문자열은 "모든 문서 일치"를 의미 하는 별표입니다.

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ 쿼리 문자열과 **$filter** 의 조합에서 필터가 하위 집합을 만들면 쿼리 문자열이 필터링된 하위 집합에 대해 전체 텍스트 검색에 용어 입력을 제공합니다. 용어를 추가 (이동 거리 극장) 하면 결과에 검색 점수가 도입 됩니다. 여기에서 용어와 가장 일치 하는 문서는 더 높은 순위를 갖습니다. 쿼리 문자열에 필터를 사용 하는 것은 가장 일반적인 사용 패턴입니다.

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>4 개 또는 5 개의 별모양에서 5 개 보다 높은 등급의 hostels를 찾습니다.
   $filter = 검색. ismatchscoring 매기기 (' hostel ') 및 등급 ge 4 또는 검색. ismatchscoring 매기기 (' motel ') 및 등급 eq 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>설명 필드의 ' luxury ' 또는 ' high-end '에 대해 일치 하는 항목 또는 ' Luxury '와 정확 하 게 일치 하는 항목을 찾습니다.
   $filter = 검색. ismatchscoring 매기기 (' luxury | high end ', ' Description ') 또는 Category eq ' Luxury ' &$count = true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter = 검색. ismatchscoring 매기기 (' pool ') 및 등급 ge 4

  검색 = 풀&$filter = 등급 ge 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>필터링 가능한 기존 필드 만들기

필터링 가능 하도록 기존 필드를 수정할 수 없습니다. 대신 새 필드를 추가 하거나 인덱스를 다시 작성 해야 합니다. 인덱스 또는 늘이기 필드를 다시 작성 하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search 인덱스를 다시 작성 하는 방법](search-howto-reindex.md)을 참조 하세요.

## <a name="text-filter-fundamentals"></a>텍스트 필터 기본 사항

텍스트 필터는 필터에 제공 하는 리터럴 문자열과 문자열 필드를 일치 시킵니다. `$filter=Category eq 'Resort and Spa'`

전체 텍스트 검색과 달리 텍스트 필터에 대 한 어휘 분석 또는 단어 분리는 없으므로 정확한 일치 항목만 비교 합니다. 예를 들어 *f* 필드가 "sunny day"를 포함 하는 경우는 `$filter=f eq 'Sunny'` 일치 하지 않지만는 일치 한다고 가정 `$filter=f eq 'sunny day'` 합니다. 

텍스트 문자열은 대/소문자를 구분합니다. 대문자 단어의 소문자는 구분 되지 않습니다. `$filter=f eq 'Sunny day'` "sunny day"를 찾지 않습니다.

### <a name="approaches-for-filtering-on-text"></a>텍스트 필터링 방법

| 접근 방식 | Description | 사용 시기 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 구분 기호로 분리 된 문자열 목록에 대해 필드와 일치 하는 함수입니다. | 문자열 필드와 일치 해야 하는 원시 텍스트 값이 많은 필터 및 [보안 필터](search-security-trimming-for-azure-search.md) 에 권장 됩니다. **Search.in** 함수는 속도를 위해 디자인 되었으며 및를 사용 하 여 각 문자열과 필드를 명시적으로 비교 하는 것 보다 훨씬 빠릅니다 `eq` `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 동일한 필터 식에서 전체 텍스트 검색 작업과 엄격한 부울 필터 작업을 혼합할 수 있게 해주는 함수입니다. | 하나의 요청에서 여러 검색 필터 조합을 사용 하려면 **ismatch** (또는 해당 하는 해당 점수 **매기기)를 사용 합니다.** 크기가 더 큰 문자열 내에서 부분적으로 문자열을 필터링하기 위해 *contains* 필터에 대해 이를 사용할 수도 있습니다. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 필드, 연산자 및 값으로 구성된 사용자 정의 식입니다. | 문자열 필드와 문자열 값 사이에 정확히 일치 하는 항목을 찾으려면이를 사용 합니다. |

## <a name="numeric-filter-fundamentals"></a>숫자 필터 기본 사항

숫자 필드는 전체 텍스트 검색의 컨텍스트에서 `searchable`이 아닙니다. 문자열만 전체 텍스트 검색이 가능합니다. 예를 들어, 검색어로 99.99를 입력하면 $99.99로 책정된 항목이 반환되지 않고 문서의 문자열 필드에 숫자 99가 있는 항목이 표시됩니다. 따라서 숫자 데이터가 있는 경우 범위, 패싯, 그룹 등을 포함하여 필터에 사용한다고 가정합니다. 

숫자 필드(가격, 크기, SKU, ID)를 포함하는 문서는 필드가 `retrievable`로 표시되면 검색 결과에서 해당 값을 제공합니다. 여기서 핵심은 전체 텍스트 검색 자체가 숫자 필드 유형에는 적용되지 않는다는 것입니다.

## <a name="next-steps"></a>다음 단계

먼저 포털의 **검색 탐색기** 에서 **$filter** 매개 변수를 사용하여 쿼리를 제출합니다. 필터링된 다음 쿼리를 검색 창에 붙여넣으면 [부동산 샘플 인덱스](search-get-started-portal.md)에서 해당 쿼리에 대한 흥미로운 결과를 제공합니다.

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

더 많은 예제를 사용하려면 [OData 필터 식 구문 > 예제](./search-query-odata-filter.md#examples)를 참조하세요.

## <a name="see-also"></a>참조

+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [문서 검색 REST API](/rest/api/searchservice/search-documents)
+ [단순 쿼리 구문](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 쿼리 구문](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [지원 되는 데이터 형식](/rest/api/searchservice/supported-data-types)