---
title: Jak zarządzać cyklem życia urządzeń w usłudze Azure AD | Microsoft Docs
description: Dowiedz się, jak zarządzanie urządzeniami może pomóc w sprawowaniu kontroli nad urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/03/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 53474d2d364b26f4d54e1917e4aa50bc6f0e5280
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449592"
---
# <a name="how-to-manage-the-stale-devices-in-azure-ad"></a>Instrukcje: zarządzanie nieaktywnymi urządzeniami w usłudze Azure AD

W idealnym przypadku, aby zakończyć cykl życia, zarejestrowane urządzenia powinny zostać wyrejestrowane, gdy nie są już potrzebne. Ale czasami, na przykład z powodu zgubienia, kradzieży lub uszkodzenia urządzenia albo ponownej instalacji systemu operacyjnego, w Twoim środowisku pojawiają się nieaktywne urządzenia. Jako administrator IT prawdopodobnie potrzebujesz metody usuwania takich nieaktywnych urządzeń, aby Twoje zasoby mogły skoncentrować się na zarządzaniu urządzeniami, które faktycznie wymagają zarządzania.

W tym artykule dowiesz się, jak skutecznie zarządzać nieaktywnymi urządzeniami we własnym środowisku.
  

## <a name="what-is-a-stale-device"></a>Co to jest nieaktywne urządzenie?

Nieaktywnym nazywamy urządzenie, które zostało zarejestrowane za pomocą usługi Azure AD, ale nie było używane do uzyskiwania dostępu do aplikacji w chmurze w określonym przedziale czasu. Nieaktywne urządzenia wpływają na możliwość obsługi urządzeń i użytkowników oraz zarządzania nimi w ramach dzierżawy z następujących powodów: 

- Zduplikowane urządzenia mogą utrudnić pracownikom działu pomocy technicznej zidentyfikowanie urządzenia, które jest aktualnie aktywne.

- Zwiększona liczba urządzeń powoduje tworzenie zbędnych zapisów zwrotnych dla urządzeń, co wydłuża czas synchronizacji programu AAD Connect.

- W celu zachowania porządku i spełnienia wymagań dotyczących zgodności warto mieć „czysty” stan urządzeń. 


Istnienie nieaktywnych urządzeń w usłudze Azure AD może być niezgodne z ogólnymi zasadami cyklu życia urządzeń w Twojej organizacji.



## <a name="detect-stale-devices"></a>Wykrywanie nieaktywnych urządzeń

Ponieważ nieaktywne urządzenie jest zdefiniowane jako zarejestrowane urządzenie, które nie było używane do uzyskiwania dostępu do aplikacji w chmurze w określonym przedziale czasu, wykrywanie nieaktywnych urządzeń wymaga właściwości powiązanej ze znacznikiem czasu. W usłudze Azure AD ta właściwość nazywa się **ApproximateLastLogonTimestamp** lub **znacznik czasu aktywności**. Jeśli różnica między bieżącą datą a wartością **znacznika czasu aktywności** przekroczy przedział czasu zdefiniowany dla aktywnych urządzeń, urządzenie jest uznawane za nieaktywne. Ten **znacznik czasu aktywności** znajduje się obecnie w publicznej wersji zapoznawczej.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Jak jest zarządzana wartość znacznika czasu aktywności?  

Obliczanie znacznika czasu aktywności jest wyzwalane przez próbę uwierzytelnienia urządzenia. Usługa Azure AD oblicza znacznik czasu aktywności w następujących sytuacjach:

- Wyzwolenie zasad dostępu warunkowego wymagających [urządzeń zarządzanych](../conditional-access/require-managed-devices.md) lub [zatwierdzonych aplikacji klienckich](../conditional-access/app-based-conditional-access.md).

- Wykazywanie aktywności w sieci przez urządzenia z systemem Windows 10, które są dołączone do usługi Azure AD lub dołączone hybrydowo do usługi Azure AD. 

- Zaewidencjonowanie w usłudze urządzeń zarządzanych przy użyciu usługi Intune.


Jeśli różnica między istniejącą wartością znacznika czasu aktywności a bieżącą wartością wynosi więcej niż 14 dni, istniejąca wartość jest zastępowana przez nową wartość.
    

## <a name="how-do-i-get-the-activity-timestamp"></a>Jak mogę uzyskać znacznik czasu aktywności?

Istnieją dwie możliwości uzyskania wartości znacznika czasu aktywności:

- Kolumna **Aktywność** na [stronie urządzeń](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) w witrynie Azure Portal

    ![Znacznik czasu aktywności](./media/manage-stale-devices/01.png)


- Polecenie cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

    ![Znacznik czasu aktywności](./media/manage-stale-devices/02.png)



## <a name="plan-the-cleanup-of-your-stale-devices"></a>Planowanie oczyszczania nieaktywnych urządzeń

Aby skutecznie oczyścić nieaktywne urządzenia w swoim środowisku, zdefiniuj powiązane zasady. Te zasady pomagają zapewnić uwzględnienie wszystkich zagadnień dotyczących nieaktywnych urządzeń. Poniższe sekcje zawierają przykłady typowych zagadnień uwzględnianych w zasadach. 

### <a name="cleanup-account"></a>Konto oczyszczania

Aby zaktualizować urządzenie w usłudze Azure AD, potrzebne jest konto, które ma przypisaną jedną z następujących ról:

- Administrator globalny

- Administrator urządzenia w chmurze (Nowa rola jest już dostępna!)

- Administrator usługi Intune

W zasadach oczyszczania wybierz konta, które mają przypisane wymagane role. 


### <a name="timeframe"></a>Przedział czasu

Zdefiniuj przedział czasu, który jest wskaźnikiem służącym do wykrywania nieaktywnego urządzenia. Podczas definiowania przedziału czasu uwzględnij 14-dniowy okres na aktualizowanie znacznika czasu aktywności. Nie powinno się używać znacznika czasu krótszego niż 14 dni do wykrywania nieaktywnego urządzenia. Istnieją sytuacje, w których urządzenie może wyglądać na nieaktywne, chociaż tak nie jest. Na przykład gdy właściciel urządzenia jest na urlopie lub na zwolnieniu lekarskim   przekraczającym przedział czasu zdefiniowany dla nieaktywnych urządzeń.

### <a name="disable-devices"></a>Wyłączanie urządzeń

Nie zaleca się natychmiastowego usuwania urządzenia, które wydaje się być nieaktualne, ponieważ takiego usunięcia nie można cofnąć, gdy uznanie urządzenia za nieaktywne okaże się wynikiem fałszywie dodatnim. Najlepszym rozwiązaniem jest wyłączenie urządzenia na okres prolongaty przed jego usunięciem. W zasadach zdefiniuj przedział czasu wyłączenia urządzenia przed jego usunięciem.


### <a name="mdm-controlled-devices"></a>Urządzenia kontrolowane przez rozwiązanie MDM

Jeśli urządzenie jest kontrolowane przez usługę Intune lub dowolne inne rozwiązanie do zarządzania urządzeniami mobilnymi (MDM), wycofaj to urządzenie z takiego rozwiązania przed jego wyłączeniem lub usunięciem.


### <a name="system-managed-devices"></a>Urządzenia zarządzane przez system

Nie należy usuwać urządzeń zarządzanych przez system. Są to zazwyczaj urządzenia takie jak autopilot. Po usunięciu takich urządzeń nie można ich ponownie aprowizować. Nowe polecenie cmdlet `get-msoldevice` domyślnie wyklucza urządzenia zarządzane przez system. 


### <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

W przypadku urządzeń dołączonych hybrydowo do usługi Azure AD powinny być przestrzegane lokalne zasady zarządzania nieaktywnymi urządzeniami. 

Oczyszczanie w usłudze Azure AD:

- **Urządzenia z systemem Windows 10** — wyłącz lub usuń urządzenia z systemem Windows 10 w lokalnej usłudze AD i pozwól usłudze Azure AD Connect zsynchronizować zmieniony stan urządzenia z usługą Azure AD.

- **System Windows 7 lub 8** — wyłącz lub usuń urządzenia z systemem Windows 7 lub 8 w usłudze Azure AD. Nie możesz używać programu Azure AD Connect do wyłączania ani usuwania urządzeń z systemem Windows 7 lub 8 w usłudze Azure AD.



### <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Urządzenia dołączone do usługi Azure AD wyłącza się lub usuwa w usłudze Azure AD.


### <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane w usłudze Azure AD

Urządzenia zarejestrowane w usłudze Azure AD wyłącza się lub usuwa w usłudze Azure AD.



## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Oczyszczanie nieaktywnych urządzeń w witrynie Azure Portal  

Nieaktywne urządzenia można oczyszczać w witrynie Azure Portal, ale bardziej wydajne jest wykonywanie tego za pomocą skryptu programu PowerShell. Użyj najnowszego modułu programu PowerShell w wersji 1, aby za pomocą filtru znacznika czasu odfiltrować urządzenia zarządzane przez system, takie jak autopilot. W tym przypadku użycie programu PowerShell w wersji 2 nie jest zalecane.


Typowa procedura obejmuje następujące czynności:

1. Nawiązywanie połączenia z usługą Azure Active Directory przy użyciu polecenia cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)

2. Pobieranie listy urządzeń

3. Wyłączanie urządzenia przy użyciu polecenia cmdlet [Disable-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0) 

4. Przed usunięciem urządzenia poczekaj, aż upłynie wybrana przez Ciebie liczba dni okresu prolongaty.

5. Usuwanie urządzenia przy użyciu polecenia cmdlet [Remove-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0)

### <a name="get-the-list-of-devices"></a>Pobieranie listy urządzeń

Aby uzyskać listę wszystkich urządzeń i zachować zwrócone dane w pliku CSV, użyj następującego polecenia:

```powershell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Jeśli masz dużą liczbę urządzeń w katalogu, użyj filtru znacznika czasu, aby zawęzić liczbę zwróconych urządzeń. Aby uzyskać wszystkie urządzenia ze znacznikiem czasu starszym niż określona data oraz zachować zwrócone dane w pliku CSV, użyj następującego polecenia: 

```powershell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```


## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Dlaczego znacznik czasu nie jest aktualizowany częściej?

Znacznik czasu jest aktualizowany w celu obsługi scenariuszy cyklu życia urządzenia. Nie jest to inspekcja. Aby wykonywać częstsze aktualizacje na urządzeniu, użyj dzienników inspekcji logowania.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Dlaczego muszę zadbać o klucze funkcji BitLocker?

Skonfigurowane klucze funkcji BitLocker dla urządzeń z systemem Windows 10 są przechowywane w obiekcie urządzenia w usłudze Azure AD. Jeśli usuwasz nieaktywne urządzenie, usuwasz również klucze funkcji BitLocker, które są przechowywane na tym urządzeniu. Przed usunięciem nieaktywnego urządzenia musisz sprawdzić, czy zasady oczyszczania są zgodne z rzeczywistym cyklem życia urządzenia. 

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Jak mogę sprawdzić, czy wszystkie typy urządzeń zostały dołączone?

Aby dowiedzieć się więcej na temat różnych typów, zobacz [omówienie zarządzania urządzeniami](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Co się stanie, gdy urządzenie zostanie wyłączone?

Każde uwierzytelnianie z użyciem urządzenia w usłudze Azure AD jest odrzucane. Typowe przykłady:

- **Urządzenie dołączone hybrydowo do usługi Azure AD** — użytkownicy mogą korzystać z urządzenia, aby zalogować się do swojej domeny lokalnej. Nie mogą jednak uzyskać dostępu do zasobów usługi Azure AD, takich jak usługa Office 365.

- **Urządzenia dołączone do usługi Azure AD** — użytkownicy nie mogą używać urządzenia do logowania. 

- **Urządzenia przenośne** — użytkownicy nie mogą uzyskać dostępu do zasobów usługi Azure AD, takich jak usługa Office 365. 



## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure Portal, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md)



