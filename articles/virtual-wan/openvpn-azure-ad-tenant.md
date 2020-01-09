---
title: 'VPN Gateway: dzierżawa usługi Azure AD dla połączeń sieci VPN P2S: uwierzytelnianie usługi Azure AD'
description: Aby nawiązać połączenie z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD, możesz użyć sieci VPN P2S
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: alzam
ms.openlocfilehash: 56226bf0310e51e62fa814b838f157a4e16a9d10
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530718"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Tworzenie dzierżawy Azure Active Directory dla połączeń protokołu OpenVPN P2S

Podczas nawiązywania połączenia z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania usługi RADIUS. Jednak w przypadku korzystania z otwartego protokołu sieci VPN można również użyć uwierzytelniania Azure Active Directory. Ten artykuł ułatwia skonfigurowanie dzierżawy usługi Azure AD na potrzeby P2S Open VPN Authentication.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>

## <a name="tenant"></a>1. Tworzenie dzierżawy usługi Azure AD

Utwórz dzierżawę usługi Azure AD, wykonując czynności opisane w artykule [Tworzenie nowej dzierżawy](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nazwa organizacji
* Początkowa nazwa domeny

Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. tworzenie użytkowników dzierżawy usługi Azure AD

Następnie Utwórz dwa konta użytkowników. Utwórz jedno konto administratora globalnego i jedno konto użytkownika głównego. Główne konto użytkownika jest używane jako główne konto osadzania (konto usługi). Podczas tworzenia konta użytkownika dzierżawy usługi Azure AD można dostosować rolę katalogu dla typu użytkownika, który ma zostać utworzony.

Wykonaj kroki opisane w [tym artykule](../active-directory/fundamentals/add-users-azure-active-directory.md) , aby utworzyć co najmniej dwóch użytkowników dla dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu** w celu utworzenia typów kont:

* Administrator globalny
* Użytkownik

## <a name="enable-authentication"></a>3. Włącz uwierzytelnianie usługi Azure AD na bramie sieci VPN

1. Znajdź identyfikator katalogu katalogu, który ma być używany do uwierzytelniania. Jest on wymieniony w sekcji właściwości na stronie Active Directory.

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Skopiuj identyfikator katalogu.

3. Zaloguj się do Azure Portal jako użytkownik, do którego przypisano rolę **administratora globalnego** .

4. Następnie udziel zgody administratora. Skopiuj i wklej adres URL odnoszący się do lokalizacji wdrożenia na pasku adresu przeglądarki:

    Publiczne

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Platforma Azure dla instytucji rządowych

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure w Chinach — 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Jeśli zostanie wyświetlony monit, wybierz konto **administratora globalnego** .

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Wybierz pozycję **Akceptuj** po wyświetleniu monitu.

    ![Zaakceptuj](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. W ramach usługi Azure AD w **aplikacjach dla przedsiębiorstw**zostanie wyświetlona lista **Azure VPN** .

    ![Sieć VPN platformy Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Skonfiguruj uwierzytelnianie usługi Azure AD dla sieci VPN użytkownika i przypisz je do koncentratora wirtualnego, wykonując czynności opisane w temacie [Konfigurowanie uwierzytelniania usługi Azure AD na potrzeby połączenia punkt-lokacja z platformą Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Następne kroki

Aby można było połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN i skojarzyć go z koncentratorem wirtualnym. Zobacz [Konfigurowanie uwierzytelniania usługi Azure AD na potrzeby połączenia punkt-lokacja z platformą Azure](virtual-wan-point-to-site-azure-ad.md).
