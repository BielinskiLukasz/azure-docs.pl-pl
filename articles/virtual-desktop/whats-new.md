---
title: Co nowego w programie Virtual Desktop systemu Windows? — Azure
description: Nowe funkcje i aktualizacje produktów dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: overview
ms.date: 08/07/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 8663fc3f016fadcd4f4c99acd800cd0ccf8844f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003525"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Co nowego w programie Virtual Desktop systemu Windows?

Regularnie aktualizacje pulpitu wirtualnego systemu Windows. W tym artykule znajdują się informacje o tym, jak:

- Najnowsze aktualizacje
- Nowe funkcje
- Ulepszenia istniejących funkcji
- Poprawki błędów

Ten artykuł jest aktualizowany co miesiąc. Upewnij się, że w tym miejscu często należy zaznaczyć, aby zachować nowe aktualizacje.

## <a name="july-2020"></a>Lipiec 2020 r.  

W lipcu była dostępna Ogólna dostępność usług pulpitu wirtualnego systemu Windows z integracją usługi Azure Resource Management.

Poniżej przedstawiono zmiany w nowej wersji: 

- Wartość "Jesień 2019 Release" jest teraz znana jako "pulpit wirtualny systemu Windows (klasyczny)", podczas gdy "2020 sprężynowe wydanie" jest teraz tylko "pulpitem wirtualnym systemu Windows". Aby uzyskać więcej informacji, zapoznaj się z [wpisem w blogu](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Aby dowiedzieć się więcej o nowych funkcjach, zapoznaj się z [wpisem w blogu](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Aktualizacja narzędzia skalowania automatycznego

Najnowsza wersja narzędzia skalowania automatycznego, która była w wersji zapoznawczej, jest teraz ogólnie dostępna. To narzędzie używa konta usługi Azure Automation i aplikacji logiki platformy Azure, aby automatycznie zamykać i ponownie uruchamiać maszyny wirtualne hosta sesji (VM) w puli hostów, zmniejszając koszty związane z infrastrukturą. Dowiedz się więcej na temat [skalowania hostów sesji przy użyciu Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

Teraz można wykonać następujące czynności w Azure Portal na pulpicie wirtualnym systemu Windows: 

- Bezpośrednie przypisywanie użytkowników do osobistych hostów sesji usług pulpitu  
- Zmień ustawienie środowiska weryfikacji dla pul hostów 

### <a name="diagnostics"></a>Diagnostyka

Opublikowano kilka nowych wstępnie skompilowanych zapytań dla obszaru roboczego Log Analytics. Aby uzyskać dostęp do zapytań, przejdź do pozycji **dzienniki** i w obszarze **kategoria**wybierz pozycję **pulpit wirtualny systemu Windows**. Dowiedz się więcej na temat [używania funkcji diagnostyki log Analytics](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Aktualizacja klienta Pulpit zdalny dla systemu Android

[Klient pulpit zdalny dla systemu Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) obsługuje teraz połączenia pulpitu wirtualnego systemu Windows. Począwszy od wersji 10.0.7, klient systemu Android jest wyposażony w nowy interfejs użytkownika w celu ulepszenia środowiska użytkownika. Klient integruje się również z Microsoft Authenticator na urządzeniach z systemem Android w celu włączenia dostępu warunkowego podczas subskrybowania obszarów roboczych pulpitu wirtualnego systemu Windows.  

Poprzednia wersja Pulpit zdalny Client jest teraz nazywana "Pulpit zdalny 8". Wszystkie istniejące połączenia w starszej wersji klienta zostaną bezproblemowo przeniesione do nowego klienta. Nowy klient został ponownie zapisany w tym samym podstawowym aparacie protokołu RDP jako klienci z systemem iOS i macOS, co pozwala na szybsze wydanie nowych funkcji na wszystkich platformach. 

### <a name="teams-update"></a>Aktualizacja zespołów

Wprowadziliśmy ulepszenia usługi Microsoft Teams dla pulpitu wirtualnego systemu Windows. Co najważniejsze, pulpit wirtualny systemu Windows obsługuje teraz optymalizację audio i wideo dla klienta klasycznego systemu Windows. Przekierowanie zwiększa czas oczekiwania przez tworzenie bezpośrednich ścieżek między użytkownikami, gdy używają dźwięku lub wideo w wywołaniach i spotkaniach. Mniejsza odległość oznacza mniejszą liczbę przeskoków, co sprawia, że wywołania wyglądają i wygładzają dźwięk. Dowiedz się więcej na [temat używania zespołów na pulpicie wirtualnym systemu Windows](teams-on-wvd.md).

## <a name="june-2020"></a>Czerwiec 2020 r.

W ciągu ostatniego miesiąca wprowadziliśmy pulpit wirtualny systemu Windows z integracją Azure Resource Manager w wersji zapoznawczej. Ta aktualizacja ma wiele ciekawych nowych funkcji, które chcielibyśmy poznać. W tym miejscu nowości nowe dla tej wersji pulpitu wirtualnego systemu Windows.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Pulpit wirtualny systemu Windows jest teraz zintegrowany z usługą Azure Resource Manager

Pulpit wirtualny systemu Windows jest teraz zintegrowany z usługą Azure Resource Manager. W najnowszej aktualizacji wszystkie obiekty pulpitu wirtualnego systemu Windows są teraz Azure Resource Manager zasoby. Ta aktualizacja jest również zintegrowana z kontrolą dostępu opartą na rolach (Azure RBAC). Zobacz [co to jest Azure Resource Manager?](../azure-resource-manager/management/overview.md) , aby dowiedzieć się więcej.

Oto, co to jest ta zmiana:

- Pulpit wirtualny systemu Windows jest teraz zintegrowany z Azure Portal. Oznacza to, że możesz zarządzać wszystko bezpośrednio w portalu, nie musisz mieć wymaganych narzędzi PowerShell, Web Apps ani innych firm. Aby rozpocząć, zapoznaj się z naszym samouczkiem w witrynie [Tworzenie puli hostów za pomocą Azure Portal](create-host-pools-azure-marketplace.md).

- Przed tą aktualizacją można publikować tylko usługi RemoteApp i pulpity dla poszczególnych użytkowników. Za pomocą Azure Resource Manager można teraz publikować zasoby w Azure Active Directory grupach.

- Starsza wersja pulpitu wirtualnego systemu Windows ma cztery wbudowane role administracyjne, które można przypisać do dzierżawy lub puli hostów. Role te działają teraz w [kontroli dostępu opartej na rolach platformy Azure (Azure RBAC)](../role-based-access-control/overview.md). Te role można zastosować do każdego obiektu Azure Resource Manager pulpitu wirtualnego systemu Windows, który umożliwia posiadanie pełnego, rozbudowanego modelu delegowania.

- W ramach tej aktualizacji nie jest już konieczne wielokrotne uruchamianie szablonu Azure Marketplace lub GitHub, aby rozszerzyć pulę hostów. Wystarczy rozszerzyć pulę hostów, aby przejść do puli hostów w Azure Portal i wybrać pozycję **+ Dodaj** , aby wdrożyć dodatkowe hosty sesji.

- Wdrożenie puli hostów jest obecnie w pełni zintegrowane z [galerią udostępnionych obrazów systemu Azure](../virtual-machines/windows/shared-image-galleries.md). Galeria obrazów udostępnionych to oddzielna usługa platformy Azure, która przechowuje definicje obrazów maszyn wirtualnych (VM), w tym przechowywanie wersji obrazu. Można również użyć replikacji globalnej do kopiowania i wysyłania obrazów do innych regionów świadczenia usługi Azure na potrzeby lokalnego wdrożenia.

- Funkcje monitorowania, które zostały użyte do wykonania za pomocą programu PowerShell lub aplikacji internetowej usługi diagnostycznej, zostały przeniesione do Log Analytics w Azure Portal. Dostępne są również dwie opcje wizualizacji raportów. Można uruchamiać zapytania Kusto i używać skoroszytów do tworzenia raportów wizualnych.

- Nie musisz już wykonywać Azure Active Directory (Azure AD), aby wyrazić zgodę na korzystanie z pulpitu wirtualnego systemu Windows. W tej aktualizacji dzierżawa usługi Azure AD w ramach subskrypcji platformy Azure uwierzytelnia użytkowników i udostępnia kontrolki kontroli RBAC platformy Azure dla administratorów.


### <a name="powershell-support"></a>Obsługa programu PowerShell

Dodaliśmy nowe polecenia cmdlet AzWvd do Azure PowerShell AZ module z tą aktualizacją. Ten nowy moduł jest obsługiwany w programie PowerShell Core, który działa na platformie .NET Core.

Aby zainstalować moduł, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie modułu programu PowerShell dla pulpitu wirtualnego systemu Windows](powershell-module.md).

Listę dostępnych poleceń można także znaleźć w [dokumentacji programu PowerShell AzWvd](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Aby uzyskać więcej informacji o nowych funkcjach, zapoznaj się z [naszym wpisem w blogu](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Dodatkowe bramy

Dodaliśmy nowy klaster bramy w Republice Południowej Afryki, aby zmniejszyć opóźnienie połączenia.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams na pulpicie wirtualnym systemu Windows (wersja zapoznawcza)

Wprowadziliśmy kilka ulepszeń usługi Microsoft Teams dla systemu Windows Virtual Desktop. Co najważniejsze, pulpit wirtualny systemu Windows obsługuje teraz funkcję przekierowywania audio i wizualizacji dla wywołań. Przekierowanie zwiększa czas oczekiwania przez tworzenie bezpośrednich ścieżek między użytkownikami podczas wywoływania przy użyciu dźwięku lub wideo. Mniejsza odległość oznacza mniejszą liczbę przeskoków, co sprawia, że wywołania wyglądają i wygładzają dźwięk.

Aby dowiedzieć się więcej, zobacz [nasz wpis w blogu](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z przyszłymi planami w [Microsoft 365 Windows Virtual Desktop plan](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).

Zapoznaj się z tymi artykułami, aby dowiedzieć się więcej o aktualizacjach naszych klientów dla pulpitu wirtualnego systemu Windows i Usługi pulpitu zdalnego:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Sieć Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
