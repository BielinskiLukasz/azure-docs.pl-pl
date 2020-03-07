---
title: Dostęp do rozwiązania Azure VMware według CloudSimple — Portal
description: Zawiera opis sposobu uzyskiwania dostępu do rozwiązania VMware Solution by CloudSimple z witryny Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d27bab243f6805436465f5027e519d33e20f6f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384206"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Dostęp do rozwiązania VMware przez portal CloudSimple z poziomu Azure Portal

Logowanie jednokrotne jest obsługiwane w przypadku dostępu do portalu CloudSimple. Po zalogowaniu się do Azure Portal można uzyskać dostęp do portalu CloudSimple bez konieczności ponownego logowania. Podczas pierwszego uzyskiwania dostępu do portalu CloudSimple zostanie wyświetlony monit o autoryzację aplikacji [autoryzacji usługi CloudSimple](#consent-to-cloudsimple-service-authorization-application) .  Autoryzacja to jednorazowa akcja.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Użytkownicy z rolami wbudowanego **właściciela** i **współautor** mogą uzyskać dostęp do portalu CloudSimple.  Role należy skonfigurować w grupie zasobów, w której wdrożono usługę CloudSimple.  Role można także skonfigurować w obiekcie usługi CloudSimple.  Aby uzyskać więcej informacji na temat sprawdzania roli, zobacz artykuł [Wyświetlanie przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/check-access) . Tylko użytkownicy z wbudowanymi rolami **właściciela** i **współautorów** mogą uzyskać dostęp do portalu CloudSimple.  Role muszą być skonfigurowane w ramach subskrypcji.  Aby uzyskać więcej informacji na temat sprawdzania roli, zobacz artykuł [Wyświetlanie przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

W przypadku korzystania z ról niestandardowych rola powinna mieć dowolną z następujących operacji w obszarze ```Actions```.  Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [role niestandardowe dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Jeśli którakolwiek z operacji jest częścią ```NotActions```, użytkownik nie będzie mógł uzyskać dostępu do portalu CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usługi CloudSimple Services**.

3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.

4. Na stronie **Przegląd** kliknij pozycję **Przejdź do portalu CloudSimple**.  Jeśli po raz pierwszy uzyskujesz dostęp do portalu CloudSimple z poziomu Azure Portal, zostanie wyświetlony monit o autoryzację aplikacji [autoryzacji usługi CloudSimple](#consent-to-cloudsimple-service-authorization-application) . 

    ![Uruchom Portal CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> W przypadku wybrania operacji chmury prywatnej (takiej jak utworzenie lub rozszerzenie chmury prywatnej) bezpośrednio z Azure Portal Portal CloudSimple zostanie otwarty na wskazanej stronie.

W portalu CloudSimple wybierz pozycję **Strona główna** w menu bocznym, aby wyświetlić podsumowanie informacji o chmurach prywatnych. Wyświetlane są zasoby i pojemność chmur prywatnych wraz z alertami i zadaniami, które wymagają uwagi. W przypadku typowych zadań kliknij nazwane ikony w górnej części strony.

![Strona główna](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Wyrażanie zgody na aplikację autoryzacji usługi CloudSimple

Po pierwszym uruchomieniu Portal CloudSimple z Azure Portal wymaga zgody użytkownika na aplikację autoryzacji usługi CloudSimple.  Wybierz pozycję **Akceptuj** , aby udzielić żądanych uprawnień i uzyskać dostęp do portalu CloudSimple.

![Wyrażanie zgody na autoryzację usługi CloudSimple — Administratorzy](media/cloudsimple-azure-consent.png)

Jeśli masz uprawnienia administratora globalnego, możesz wyrazić zgodę na Twoją organizację.  Wybierz pozycję **wyrażanie zgody w imieniu organizacji**.

![Wyrażanie zgody na autoryzację usługi CloudSimple — Administrator globalny](media/cloudsimple-azure-consent-global-admin.png)

Jeśli Twoje uprawnienia nie zezwalają na dostęp do portalu CloudSimple, skontaktuj się z administratorem globalnym dzierżawy, aby przyznać wymagane uprawnienia.  Administrator globalny może wyrazić zgodę w imieniu organizacji.

![Wyrażanie zgody na autoryzację usługi CloudSimple — wymaga od administratorów](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)
