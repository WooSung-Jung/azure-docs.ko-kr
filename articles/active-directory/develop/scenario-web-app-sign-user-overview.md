---
title: 웹 앱에서 사용자 로그인 | Microsoft
titleSuffix: Microsoft identity platform
description: 사용자를 로그인 하는 웹 앱을 빌드하는 방법 알아보기 (개요)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0bbc799f946d318c305a96d9cb8c6831d9242ff6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578297"
---
# <a name="scenario-web-app-that-signs-in-users"></a>시나리오: 사용자를 로그인하는 웹앱

Microsoft id 플랫폼을 사용 하 여 사용자를 로그인 하는 웹 앱을 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="getting-started"></a>시작

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

사용자를 로그인 하는 첫 번째 이식 가능 (ASP.NET Core) 웹 앱을 만들려면 다음 빠른 시작을 수행 합니다.

[빠른 시작: 사용자에 게 로그인 하는 웹 앱 ASP.NET Core](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

기존 ASP.NET 웹 응용 프로그램에 로그인을 추가 하는 방법을 이해 하려면 다음 빠른 시작을 시도 합니다.

[빠른 시작: 사용자에 게 로그인 하는 ASP.NET 웹 앱](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Java 개발자 인 경우 다음 빠른 시작을 시도 합니다.

[빠른 시작: Java 웹앱에 Microsoft로 로그인 추가](quickstart-v2-java-webapp.md)

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Node.js 개발자 인 경우 다음 빠른 시작을 시도 합니다.

[빠른 시작: Node.js 웹 앱에 Microsoft에 로그인 추가](quickstart-v2-nodejs-webapp-msal.md)

# <a name="python"></a>[Python](#tab/python)

Python을 사용 하 여 개발 하는 경우 다음 빠른 시작을 사용해 보세요.

[빠른 시작: Python 웹앱에 Microsoft로 로그인 추가](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>개요

사용자가 로그인 할 수 있도록 웹 앱에 인증을 추가 합니다. 인증을 추가 하면 사용자에 대 한 환경을 사용자 지정 하기 위해 웹 앱에서 제한 된 프로필 정보에 액세스할 수 있습니다.

웹 앱은 웹 브라우저에서 사용자를 인증 합니다. 이 시나리오에서는 웹 앱이 사용자의 브라우저에서 Azure Active Directory (Azure AD)에 로그인 하도록 지시 합니다. Azure AD는 보안 토큰의 사용자에 대 한 클레임을 포함 하는 사용자의 브라우저를 통해 로그인 응답을 반환 합니다. 사용자 로그인은 미들웨어 [라이브러리](scenario-web-app-sign-user-app-configuration.md#microsoft-libraries-supporting-web-apps)를 사용 하 여 간소화 된 [Open ID Connect](./v2-protocols-oidc.md) 표준 프로토콜을 활용 합니다.

![웹앱의 사용자 로그인](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

두 번째 단계로 응용 프로그램에서 로그인 한 사용자 대신 웹 Api를 호출할 수 있도록 설정할 수 있습니다. 다음 단계는 웹 [api를 호출 하는 웹 앱](scenario-web-app-call-api-overview.md)에서 찾을 수 있는 다른 시나리오입니다.

> [!NOTE]
> 웹 앱에 로그인을 추가 하는 작업은 웹 앱을 보호 하 고  **미들웨어** 라이브러리인 사용자 토큰의 유효성을 검사 하는 것입니다. .NET의 경우이 시나리오에는 보호 된 Api를 호출 하는 토큰을 획득 하는 것에 대 한 MSAL (Microsoft Authentication Library)이 아직 필요 하지 않습니다. 웹 앱이 web Api를 호출 해야 하는 경우 추가 작업 시나리오에서 .NET 용 인증 라이브러리를 소개 합니다.

## <a name="specifics"></a>특수 적용 사항

- 응용 프로그램을 등록 하는 동안 여러 위치에 앱을 배포 하는 경우 회신 Uri를 하나 이상 제공 해야 합니다. 경우에 따라 (ASP.NET 및 ASP.NET Core) ID 토큰을 사용 하도록 설정 해야 합니다. 마지막으로 응용 프로그램이 로그 아웃 하는 사용자에 게 반응 하도록 로그 아웃 URI를 설정 하는 것이 좋습니다.
- 응용 프로그램에 대 한 코드에서 웹 앱이 로그인을 위임 하는 권한을 제공 해야 합니다. 토큰 유효성 검사를 사용자 지정 하는 것이 좋습니다 (특히 파트너 시나리오에서).
- 웹 응용 프로그램은 모든 계정 유형을 지원 합니다. 자세한 내용은 [지원 되는 계정 유형](v2-supported-account-types.md)을 참조 하세요.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이 시나리오의 다음 문서 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore)으로 이동 합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

이 시나리오의 다음 문서 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet)으로 이동 합니다.

# <a name="java"></a>[Java](#tab/java)

이 시나리오의 다음 문서 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=java)으로 이동 합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 시나리오의 다음 문서 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=nodejs)으로 이동 합니다.

# <a name="python"></a>[Python](#tab/python)

이 시나리오의 다음 문서 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=python)으로 이동 합니다.

---
