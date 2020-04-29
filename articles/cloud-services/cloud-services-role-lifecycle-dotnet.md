---
title: Obsługa zdarzeń cyklu życia usługi w chmurze | Microsoft Docs
description: Dowiedz się, w jaki sposób można używać metod cyklu życia roli usługi w chmurze w programie .NET
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: a6030cbb756525137497834ac911835033858401
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80652092"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Dostosowywanie cyklu życia roli sieci Web lub procesu roboczego w programie .NET
Podczas tworzenia roli procesu roboczego należy zwiększyć klasę [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) , która dostarcza metody umożliwiające przesłonięcie reakcji na zdarzenia cyklu życia. W przypadku ról sieci Web Ta klasa jest opcjonalna, dlatego należy jej użyć do reagowania na zdarzenia cyklu życia.

## <a name="extend-the-roleentrypoint-class"></a>Poszerzenie klasy RoleEntryPoint
Klasa [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) zawiera metody, które są wywoływane przez platformę Azure podczas **uruchamiania**, **uruchamiania**lub **zatrzymywania** roli sieci Web lub procesu roboczego. Opcjonalnie można zastąpić te metody, aby zarządzać inicjalizacją roli, sekwencjami zamykania ról lub wątkiem wykonywania roli. 

Podczas rozszerzania **RoleEntryPoint**należy mieć świadomość następujących zachowań metod:

* Metoda [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) zwraca wartość logiczną, dlatego można zwrócić **false** z tej metody.
  
   Jeśli kod zwróci **wartość false**, proces roli jest nieoczekiwanie zakończony, bez uruchamiania sekwencji zamknięcia, która może być zainstalowana. Ogólnie rzecz biorąc, należy unikać zwracania **wartości false** z metody **OnStart** .
* Każdy nieprzechwycony wyjątek w ramach przeciążenia metody **RoleEntryPoint** jest traktowany jako nieobsługiwany wyjątek.
  
   Jeśli wystąpi wyjątek w ramach jednej z metod cyklu życia, platforma Azure zgłosi zdarzenie [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) , a następnie proces zostanie przerwany. Po przełączeniu roli do trybu offline zostanie ona uruchomiona ponownie przez platformę Azure. Gdy wystąpi nieobsługiwany wyjątek, zdarzenie [zatrzymania](/previous-versions/azure/reference/ee758136(v=azure.100)) nie zostanie wywołane i Metoda **OnStop** nie zostanie wywołana.

Jeśli rola nie zostanie uruchomiona lub jest odtwarzana między Stanami inicjowania, zajętości i zatrzymywania, kod może zgłaszać nieobsługiwany wyjątek w ramach jednego z zdarzeń cyklu życia po każdym ponownym uruchomieniu roli. W takim przypadku należy użyć zdarzenia [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) do określenia przyczyny wyjątku i odpowiednio je obsłużyć. Rola może również zwracać z metody [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) , która powoduje ponowne uruchomienie roli. Aby uzyskać więcej informacji o Stanach wdrożenia, zobacz [typowe problemy, które powodują](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)odtworzenie ról.

> [!NOTE]
> Jeśli używasz **narzędzi platformy Azure dla Microsoft Visual Studio** do tworzenia aplikacji, szablony projektów ról automatycznie rozszerzają klasę **RoleEntryPoint** , w plikach *WebRole.cs* i *WorkerRole.cs* .
> 
> 

## <a name="onstart-method"></a>OnStart — metoda
Metoda **OnStart** jest wywoływana, gdy wystąpienie roli jest przełączane w tryb online przez platformę Azure. Podczas wykonywania kodu OnStart wystąpienie roli jest oznaczone jako **zajęte** i żaden ruch zewnętrzny nie zostanie skierowany do niego przez moduł równoważenia obciążenia. Można zastąpić tę metodę, aby wykonać pracę inicjalizacji, na przykład implementując procedury obsługi zdarzeń i uruchamiając [Diagnostyka Azure](cloud-services-how-to-monitor.md).

Jeśli funkcja **OnStart** zwróci **wartość true**, wystąpienie zostanie pomyślnie zainicjowane, a platforma Azure wywoła metodę **RoleEntryPoint. Run** . Jeśli funkcja **OnStart** zwraca **wartość false**, rola kończy się natychmiast, bez wykonywania żadnych planowanych sekwencji zamknięcia.

Poniższy przykład kodu pokazuje, jak zastąpić metodę **OnStart** . Ta metoda konfiguruje i uruchamia monitor diagnostyczny, gdy wystąpienie roli zostanie uruchomione i skonfiguruje transfer danych rejestrowania do konta magazynu:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop, Metoda
Metoda **OnStop** jest wywoływana po wykonaniu wystąpienia roli w trybie offline przez platformę Azure i przed wyjściem z procesu. Można zastąpić tę metodę, aby wywołać kod wymagany dla wystąpienia roli w celu wyczyszczenia.

> [!IMPORTANT]
> Kod uruchomiony w metodzie **OnStop** ma ograniczony czas na zakończenie, gdy jest wywoływany z przyczyn innych niż zamknięcie inicjowane przez użytkownika. Po upływie tego czasu proces zostanie przerwany, dlatego należy się upewnić, że kod w metodzie **OnStop** może być uruchamiany szybko lub tolerowany nieprzerwanie. Metoda **OnStop** jest wywoływana po wywołaniu zdarzenia **zatrzymania** .
> 
> 

## <a name="run-method"></a>Run — metoda
Można zastąpić metodę **Run** , aby zaimplementować długotrwały wątek dla wystąpienia roli.

Zastępowanie metody **Run** nie jest wymagane; Domyślna implementacja uruchamia wątek, który w stanie uśpienia w nieskończoność. W przypadku zastąpienia metody **Run** kod powinien blokować czas nieokreślony. Jeśli metoda **Run** zwraca metodę, rola jest automatycznie odtwarzana; Innymi słowy, platforma Azure zgłasza zdarzenie **zatrzymania** i wywołuje metodę **OnStop** , aby można było wykonać sekwencje zamknięcia przed przełączeniem roli w tryb offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementowanie metod cyklu życia ASP.NET dla roli sieci Web
Aby zarządzać sekwencjami inicjalizacji i zamykania dla roli sieci Web, można użyć metod cyklu życia ASP.NET (oprócz tych dostarczonych przez klasę **RoleEntryPoint** ). Może to być przydatne w przypadku przenoszenia istniejącej aplikacji ASP.NET na platformę Azure. Metody cyklu życia ASP.NET są wywoływane z poziomu metod **RoleEntryPoint** . Metoda **startowa aplikacji\_** jest wywoływana po zakończeniu metody **RoleEntryPoint. OnStart** . Metoda **Application\_End** jest wywoływana przed wywołaniem metody **RoleEntryPoint. OnStop** .

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [utworzyć pakiet usługi w chmurze](cloud-services-model-and-package.md).




