---
title: 'Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN: uwierzytelnianie usługi Azure AD'
description: Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: 13f012af95bb2b6098317e59e5293fb72804a6a6
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471558"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Włącz usługę Azure Multi-Factor Authentication (MFA) dla użytkowników sieci VPN

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enableauth"></a>Włącz uwierzytelnianie

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="enablesign"></a>Konfigurowanie ustawień logowania

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="peruser"></a>Opcja 1 — dostęp dla użytkownika

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="conditional"></a>Opcja 2 — dostęp warunkowy

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Następne kroki

Aby nawiązać połączenie z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie uwierzytelniania usługi Azure AD na potrzeby połączenia punkt-lokacja z platformą Azure](virtual-wan-point-to-site-azure-ad.md).
