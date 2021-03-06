---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008004"
---
| 언어/프레임 워크 | 프로젝트 설정<br/>GitHub                                                                 | 패키지                                                                                | 도달<br/>시작됨                           | 사용자 로그인                                            | 웹 Api 액세스                                                 | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [빠른 시작](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| 노드               | [MSAL 노드](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-노드](https://www.npmjs.com/package/@azure/msal-node)  | [빠른 시작](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [빠른 시작](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/