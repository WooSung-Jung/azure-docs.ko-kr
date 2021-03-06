---
title: 자습서 - OpenSSL을 사용하여 Azure IoT Hub에 대한 자체 서명된 인증서 만들기 | Microsoft Docs
description: 자습서 - OpenSSL을 사용하여 Azure IoT Hub에 대한 자체 서명된 X.509 인증서 만들기
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378196"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>자습서: OpenSSL을 사용하여 자체 서명된 인증서 만들기

두 개의 자체 서명된 디바이스 인증서를 사용하여 IoT Hub에 디바이스를 인증할 수 있습니다. 인증서에는 IoT 허브에 제출하는 지문(해시 값)이 포함되어 있기 때문에 이를 지문 인증이라고도 합니다. 다음 단계에서는 두 개의 자체 서명된 인증서를 만드는 방법을 설명합니다.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>1단계 - 첫 번째 인증서에 대한 키 만들기

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>2단계 - 첫 번째 인증서에 대한 CSR 만들기

메시지가 표시되면 디바이스 ID를 지정했는지 확인합니다.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>3단계 - CSR 확인

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>4단계 - 자체 서명 인증서 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>5단계 - 인증서 2에 대한 키 만들기

메시지가 표시되면 인증서 1에 사용한 것과 동일한 디바이스 ID를 지정합니다.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>6단계 - 자체 서명 인증서 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>7단계 - 인증서 1에 대한 지문 검색

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>8단계 - 인증서 2에 대한 지문 검색

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>9단계 - 새 IoT 디바이스 만들기

Azure Portal에서 IoT Hub로 이동하고 다음 특성을 가진 새 IoT 디바이스 ID를 만듭니다.

* 두 인증서의 주체 이름과 일치하는 **디바이스 ID** 를 제공합니다.
* **X.509 자체 서명** 인증 유형을 선택합니다.
* 디바이스의 기본 인증서 및 보조 인증서에서 복사한 16진수 문자열 지문을 붙여넣습니다. 16진수 문자열에 콜론 구분 기호가 없는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[인증서 인증 테스트](tutorial-x509-test-certificate.md)로 이동하여 인증서가 디바이스를 IoT Hub에 인증할 수 있는지 확인합니다.
