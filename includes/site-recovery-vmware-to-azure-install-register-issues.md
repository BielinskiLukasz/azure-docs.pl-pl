---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9919521c8cb77f23f50a8097c4e630b4467dc725
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127707"
---
### <a name="installation-failures"></a>Błędy instalacji
| **Przykładowy komunikat o błędzie** | **Zalecana akcja** |
|--------------------------|------------------------|
|BŁĄD   Nie można załadować kont. Błąd: System.IO.IOException: Nie można odczytać danych z połączenia transportowego podczas instalowania i rejestrowania serwera CS.| Upewnij się, że protokół TLS 1.0 jest włączony na komputerze. |

### <a name="registration-failures"></a>Błędy rejestracji
Błędy rejestracji można debugować, przeglądając dzienniki w folderze **%ProgramData%\ASRLogs**.

| **Przykładowy komunikat o błędzie** | **Zalecana akcja** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Komunikat: ACS50008: SAML token jest nieprawidłowy.<br>Identyfikator śledzenia: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Identyfikator korelacji: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Sygnatura czasowa: **2016-12-12 14:50:08Z<br>** | Upewnij się, że godzina zegara systemowego nie różni się o więcej niż 15 minut od czasu lokalnego. Uruchom ponownie instalatora, aby ukończyć rejestrację.|
|**09:35:27** : wyjątek DRRegistrationException podczas próby pobrania wszystkich magazyn odzyskiwania po awarii dla wybranego certyfikatu:: Zgłosił Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML token jest nieprawidłowy.<br>Identyfikator śledzenia: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Identyfikator korelacji: abe9deb8-3e64-464d-8375-36db9816427a<br>Sygnatura czasowa: **2016-05-19 01:35:39Z**<br> | Upewnij się, że godzina zegara systemowego nie różni się o więcej niż 15 minut od czasu lokalnego. Uruchom ponownie instalatora, aby ukończyć rejestrację.|
|06:28:45: Nie można utworzyć certyfikatu<br>06:28:45: Nie można kontynuować instalacji. Nie można utworzyć certyfikatu wymaganego do uwierzytelnienia w usłudze Site Recovery. Uruchom ponownie Instalatora. | Upewnij się, że uruchamiasz Instalatora jako administrator lokalny. |
