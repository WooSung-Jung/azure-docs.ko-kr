---
title: 파일 포함
description: 파일 포함
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80986703"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows 클라이언트

1. 공식 [openvpn 웹 사이트](https://openvpn.net/index.php/open-source/downloads.html)에서 openvpn 클라이언트 (버전 2.4 이상)를 다운로드 하 여 설치 합니다.
2. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 지점 및 사이트 간 구성 탭 또는 PowerShell의 ‘New-AzVpnClientConfiguration’에서 이 작업을 수행할 수 있습니다.
3. 프로필의 압축을 풉니다. 다음으로 메모장을 사용하여 OpenVPN 폴더의 *vpnconfig.ovpn* 구성 파일을 엽니다.
4. 만든 지점 및 사이트 간 클라이언트 인증서를 게이트웨이에서 P2S 구성으로 내보냅니다. 다음 문서 링크를 사용 합니다.

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 지침
   
   * [가상 WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) 지침
5. *.pfx* 에서 프라이빗 키 및 base64 지문을 추출합니다. 이 작업을 수행하는 방법에는 여러 가지가 있습니다. 머신에서 OpenSSL을 사용하는 것이 하나의 방법입니다. *profileinfo.txt* 파일은 CA 및 클라이언트 인증서에 대한 프라이빗 키 및 지문을 포함합니다. 클라이언트 인증서의 지문을 사용해야 합니다.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. 메모장에서 *profileinfo.txt* 를 엽니다. 클라이언트(자식) 인증서의 지문을 가져오려면 자식 인증서에 대한 텍스트(포함 및 사이) "-----BEGIN CERTIFICATE-----" 및 "-----END CERTIFICATE-----"를 선택하고 복사합니다. 제목=/ 줄을 확인하여 자식 인증서를 식별할 수 있습니다.
7. 3단계의 메모장에서 연 *vpnconfig.ovpn* 파일로 전환합니다. 아래 표시된 섹션을 찾고 "cert" 및 "/cert" 사이의 모든 항목을 바꿉니다.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 메모장에서 *profileinfo.txt* 를 엽니다. 개인 키를 가져오려면 "-----시작 개인 키-----" 및 "-----END PRIVATE KEY-----"와 같은 텍스트를 선택 하 고 복사 합니다.
9. 메모장의 vpnconfig.ovpn 파일로 돌아가서 이 섹션을 찾습니다. "key" 및 "/key" 사이의 모든 항목을 바꾸는 프라이빗 키를 붙여넣습니다.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. 다른 필드는 변경하지 마세요. 클라이언트 입력에 채워진 구성을 사용하여 VPN에 연결합니다.
11. vpnconfig.ovpn 파일을 C:\Program Files\OpenVPN\config 폴더에 복사합니다.
12. 시스템 트레이에서 OpenVPN 아이콘을 마우스 오른쪽 단추로 클릭하고 연결을 클릭합니다.

## <a name="mac-clients"></a><a name="mac"></a>Mac 클라이언트

1. [TunnelBlick](https://tunnelblick.net/downloads.html)와 같은 openvpn 클라이언트를 다운로드 하 여 설치 합니다. 
2. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 지점 및 사이트 간 구성 탭 또는 PowerShell의 ‘New-AzVpnClientConfiguration’에서 이 작업을 수행할 수 있습니다.
3. 프로필의 압축을 풉니다. 텍스트 편집기의 OpenVPN 폴더에서 vpnconfig 구성 파일을 엽니다.
4. base64에서 P2S 클라이언트 인증서 공개 키를 사용하여 P2S 클라이언트 인증서 섹션을 채웁니다. PEM 형식의 인증서에서 .cer 파일을 열고 인증서 헤더 사이에 base64 키를 복사할 수 있습니다. 인코딩된 공개 키를 가져오는 인증서를 내보내는 방법에 대 한 정보는 다음 문서 링크를 참조 하세요.

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 지침 
   
   * [가상 WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) 지침
5. base64에서 P2S 클라이언트 인증서 프라이빗 키를 사용하여 프라이빗 키 섹션을 채웁니다. 개인 키를 추출 하는 방법에 대 한 자세한 내용은 OpenVPN 사이트의 [개인 키 내보내기](https://openvpn.net/community-resources/how-to/#pki) 를 참조 하세요.
6. 다른 필드는 변경하지 마세요. 클라이언트 입력에 채워진 구성을 사용하여 VPN에 연결합니다.
7. 프로필 파일을 두 번 클릭 하 여 Tunnelblick에서 프로필을 만듭니다.
8. 응용 프로그램 폴더에서 Tunnelblick를 시작 합니다.
9. 시스템 트레이에 있는 Tunnelblick 아이콘을 클릭 하 고 연결을 선택 합니다.

> [!IMPORTANT]
>OpenVPN 프로토콜에서는 IOS 11.0 이상 및 MacOS 10.13 이상만 지원됩니다.
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS 클라이언트

1. 앱 스토어에서 OpenVPN 클라이언트 (버전 2.4 이상)를 설치 합니다.
2. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 지점 및 사이트 간 구성 탭 또는 PowerShell의 ‘New-AzVpnClientConfiguration’에서 이 작업을 수행할 수 있습니다.
3. 프로필의 압축을 풉니다. 텍스트 편집기의 OpenVPN 폴더에서 vpnconfig 구성 파일을 엽니다.
4. base64에서 P2S 클라이언트 인증서 공개 키를 사용하여 P2S 클라이언트 인증서 섹션을 채웁니다. PEM 형식의 인증서에서 .cer 파일을 열고 인증서 헤더 사이에 base64 키를 복사할 수 있습니다. 인코딩된 공개 키를 가져오는 인증서를 내보내는 방법에 대 한 정보는 다음 문서 링크를 참조 하세요.

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 지침 
   
   * [가상 WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) 지침
5. base64에서 P2S 클라이언트 인증서 프라이빗 키를 사용하여 프라이빗 키 섹션을 채웁니다. 개인 키를 추출 하는 방법에 대 한 자세한 내용은 OpenVPN 사이트에서 [개인 키 내보내기](https://openvpn.net/community-resources/how-to/#pki) 를 참조 하세요.
6. 다른 필드는 변경하지 마세요.
7. IPhone의 메일 앱에 구성 된 전자 메일 계정에 프로필 파일 (ovpn)을 전자 메일로 보냅니다. 
8. IPhone의 메일 앱에서 전자 메일을 열고 첨부 된 파일을 탭 합니다.

    ![전자 메일 열기](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. **OpenVPN에 복사** 옵션이 표시 되지 않으면 **자세히** 를 누릅니다.

    ![자세히](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. **OpenVPN에 복사를 탭 합니다** . 

    ![OpenVPN에 복사](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. **프로필 가져오기** 페이지에서 **추가** 를 탭 합니다.

    ![추가](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. **가져온 프로필** 페이지에서 **추가** 를 탭 합니다.

    ![추가 탭](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. OpenVPN 앱을 시작 하 고 **프로필** 페이지에서 스위치를 연결 하 여 연결 합니다.

    ![연결](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux 클라이언트

1. 새 터미널 세션을 엽니다. ‘Ctrl+Alt+t’를 동시에 누르면 새 세션을 열 수 있습니다.
2. 다음 명령을 입력하여 필요한 구성 요소를 설치합니다.

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 지점 및 사이트 간 구성 탭에서 이 작업을 수행할 수 있습니다.
4. 만들고 게이트웨이의 P2S 구성에 업로드한 P2S 클라이언트 인증서를 내보냅니다. 다음 문서 링크를 사용 합니다.

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 지침 
   
   * [가상 WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) 지침
5. .pfx에서 프라이빗 키 및 base64 지문을 추출합니다. 이 작업을 수행하는 방법에는 여러 가지가 있습니다. 컴퓨터에서 OpenSSL을 사용하는 것이 하나의 방법입니다.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   *profileinfo.txt* 파일은 CA 및 클라이언트 인증서에 대한 프라이빗 키 및 지문을 포함합니다. 클라이언트 인증서의 지문을 사용해야 합니다.

6. 텍스트 편집기에서 *profileinfo.txt* 를 엽니다. 클라이언트(자식) 인증서의 지문을 가져오려면 자식 인증서에 대한 "-----BEGIN CERTIFICATE-----" 및 "-----END CERTIFICATE-----"를 포함하는 해당 항목 사이의 텍스트를 선택하고 복사합니다. 제목=/ 줄을 확인하여 자식 인증서를 식별할 수 있습니다.

7. *vpnconfig.ovpn* 파일을 열고 아래에 표시된 섹션을 찾습니다. "cert" 및 "/cert" 사이의 모든 항목을 바꿉니다.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 텍스트 편집기에서 profileinfo.txt를 엽니다. 개인 키를 가져오려면 "-----BEGIN PRIVATE KEY-----" 및 "-----END PRIVATE KEY-----"를 포함 하 여 텍스트를 선택 하 고 복사 합니다.

9. 텍스트 편집기에서 vpnconfig.ovpn 파일을 열고 이 섹션을 찾습니다. "key" 및 "/key" 사이의 모든 항목을 바꾸는 프라이빗 키를 붙여넣습니다.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. 다른 필드는 변경하지 마세요. 클라이언트 입력에 채워진 구성을 사용하여 VPN에 연결합니다.
11. 명령줄을 사용하여 연결하려면 다음 명령을 입력합니다.
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. GUI를 사용하여 연결하려면 시스템 설정으로 이동합니다.
13. **+** 새 VPN 연결을 추가 하려면 클릭 합니다.
14. **VPN 추가** 에서 **파일에서 가져오기...** 를 선택합니다.
15. 프로필 파일을 찾아보고 **열기** 를 두 번 클릭하거나 선택합니다.
16. **VPN 추가** 창에서 **추가** 를 클릭합니다.
  
    ![파일에서 가져오기](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. **네트워크 설정** 페이지 또는 시스템 트레이의 네트워크 아이콘 아래에서 VPN을 **설정** 하면 연결할 수 있습니다.
