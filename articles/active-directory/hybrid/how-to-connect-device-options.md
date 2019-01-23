---
title: 'Program Azure AD Connect: Opcje urządzenia | Dokumentacja firmy Microsoft'
description: W tym dokumencie szczegółowych informacji dostępnych w programie Azure AD Connect opcje urządzenia
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 95c631c38b975c71d5e026d33391792ee811bbc6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477077"
---
# <a name="azure-ad-connect-device-options"></a>Program Azure AD Connect: Opcje urządzenia

Poniższa dokumentacja zawiera informacje o różnych opcjach urządzeń dostępnych w programie Azure AD Connect. Program Azure AD Connect można użyć, aby skonfigurować następujące dwie operacje: 
* **Dołączenie do hybrydowej usługi Azure AD**: Jeśli w lokalnym środowisku zużycie AD a zalet usługi Azure AD, można zaimplementować urządzeń przyłączonych do usługi Azure AD hybrydowych. Te urządzenia są połączone, zarówno w usłudze Active Directory w środowisku lokalnym i usługi Azure Active Directory.
* **Zapisywanie zwrotne urządzeń**: Zapisywanie zwrotne urządzeń służy do włączania dostępu warunkowego opartego na urządzeniach z usługami AD FS (2012 R2 lub nowszy) chronione urządzeń

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfiguruj opcje urządzenia w usłudze Azure AD Connect

1.  Uruchom usługę Azure AD Connect. W **dodatkowe zadania** wybierz opcję **Konfiguruj opcje urządzenia**.  Kliknij przycisk **Dalej**.
    ![Konfiguruj opcje urządzenia](./media/how-to-connect-device-options/deviceoptions.png) 

    **Przegląd** strony wyświetli szczegóły.
    ![Omówienie](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nowe Konfiguruj opcje urządzenia jest dostępna tylko w wersji 1.1.819.0 lub nowszej.

2.  Po podaniu poświadczeń dla usługi Azure AD, możesz wybrać operacji do wykonania na stronie Opcje urządzenia.
    ![Operacje dotyczące urządzenia](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Kolejne kroki

* [Konfiguruj dołączenie do hybrydowej usługi Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Skonfiguruj / Wyłącz zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md)

