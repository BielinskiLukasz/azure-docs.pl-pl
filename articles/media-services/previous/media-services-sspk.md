---
title: Licencjonowanie zestawu portów programu Microsoft® Smooth Streaming klienta
description: Dowiedz się więcej na temat licencjonowania zestawu portów usługi Microsoft® Smooth Streaming Clienting Kit.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: 3e7742e7035f3c4f4827a2edae1ad5a705aa221f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693408"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licencjonowanie zestawu portów programu Microsoft® Smooth Streaming klienta 
## <a name="overview"></a>Przegląd
Microsoft Smooth Streaming Clienting Kit (**SSPK** for Short) to Smooth Streaming implementacja klienta, która została zoptymalizowana pod kątem pomocy producentom urządzeń osadzonych, operatorom telewizji kablowej i mobilnemu, dostawcom usług zawartości, producentom korzystającym z słuchawek, niezależni dostawcy oprogramowania (ISV) i dostawcy rozwiązań do tworzenia produktów i usług do przesyłania strumieniowego zawartości w formacie Smooth Streaming. SSPK to oparta na urządzeniach i niezależna od platformy implementacja klienta Smooth Streaming, którą można przenieść na dowolne urządzenie i platformę. 

Poniżej znajduje się architektura wysokiego poziomu, a Usługa IIS Smooth Streaming Porting Kit to Smooth Streaming implementacja klienta udostępniona przez firmę Microsoft, która obejmuje całą podstawową logikę odtwarzania Smooth Streaming zawartości. Ta zawartość jest następnie przewidziana przez partnerów dla określonego urządzenia lub platformy przez implementację odpowiednich interfejsów. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Opis
SSPK jest licencjonowane na warunkach, które oferują doskonałą wartość biznesową. Licencja SSPK zapewnia branżę z:

* Smooth Streaming źródło zestawu portówC++ 
  * implementuje funkcję klienta Smooth Streaming
  * dodaje analizę formatu, algorytmy heurystyczne, logikę buforowania itp.
* Interfejsy API aplikacji odtwarzacza 
  * Interfejsy programowania do interakcji z aplikacją odtwarzacza multimedialnego
* Interfejs warstwy abstrakcji platformy (PAL) 
  * Interfejsy programowania do interakcji z systemem operacyjnym (wątki, gniazda)
* Interfejs warstwy abstrakcji sprzętu (HAL) 
  * Interfejsy programowania do interakcji z sprzętowymi dekoderami A/V (dekodowanie, renderowanie)
* Interfejs Digital Rights Management (DRM) 
  * Interfejsy programowania do obsługi technologii DRM za pomocą warstwy abstrakcji DRM (DAL)
  * Zestaw portów Microsoft PlayReady, który jest dostarczany oddzielnie, ale integruje się z tym interfejsem. Aby uzyskać więcej informacji na temat licencjonowania urządzeń firmy Microsoft PlayReady, kliknij [tutaj](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Przykłady implementacji 
  * Przykładowa implementacja PAL dla systemu Linux
  * Implementacja przykładowej warstwy HAL dla GStreamer

## <a name="licensing-options"></a>Opcje licencjonowania
Pakiet Microsoft Smooth Streaming Clienting Kit jest dostępny dla licencji w ramach dwóch odrębnych umów licencyjnych: jeden do tworzenia Smooth Streaming produktów tymczasowych klienta i drugi do dystrybucji Smooth Streaming końcowych produktów klienta dla użytkowników końcowych.

* W przypadku producentów chipsetów, integratorów systemów lub niezależnych dostawców oprogramowania (ISV), którzy wymagają zestawu numerowania kodu źródłowego do opracowania produktów przejściowych, należy wykonać **licencję produktu** Microsoft Smooth Streaming clienting Kit.
* W przypadku producentów urządzeń lub niezależnych dostawców oprogramowania, które wymagają praw dystrybucji dla końcowych produktów klienta Smooth Streaming dla użytkowników końcowych, należy wykonać **ostateczną licencję produktu** Microsoft Smooth Streaming clienting Kit.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Client Porting Kit — tymczasowy licencji produktu
W ramach tej licencji firma Microsoft oferuje zestaw portów Smooth Streaming klienta i wymagane prawa własności intelektualnej do opracowania i dystrybucji produktów tymczasowych klienta Smooth Streaming do innych Smooth Streamingch licencji na urządzenia z zestawem portów klienta, które Dystrybuuj Smooth Streaming końcowe produkty klienta.

#### <a name="fee-structure"></a>Struktura opłat
Opłata za jednorazowe licencje w Stanach Zjednoczonych $50 000 zapewnia dostęp do zestawu portów klienta Smooth Streaming. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Licencja na produkt końcowy programu Microsoft Smooth Streaming Clienting Kit
W ramach tej licencji firma Microsoft oferuje wszystkie niepotrzebne prawa własności intelektualnej do odbierania Smooth Streaming produktów tymczasowych klienta z innych Smooth Streaming licencji usługi Smooth Streaming klienta Produkty dla użytkowników końcowych.

#### <a name="fee-structure"></a>Struktura opłat
Końcowy produkt klienta Smooth Streaming jest oferowany w ramach modelu honorarium w ramach:

* $0,10 na zadaną implementację urządzenia
* Honorarium jest ograniczone do $50 000 każdego roku
* Brak honorarium dla pierwszych 10 000 implementacji urządzeń w każdym roku 

## <a name="licensing-procedure-and-sspk-access"></a>Procedura licencjonowania i dostęp SSPK
[@No__t_1](mailto:sspkinfo@microsoft.com) e-mail wszystkich zapytań licencjonowania.

[Portal dystrybucji SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) jest dostępny dla zarejestrowanych licencji tymczasowych.

Tymczasowe i końcowe licencje SSPK mogą przesyłać pytania techniczne do [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licencje na umowę o produkcie pośrednim klienta firmy Microsoft Smooth Streaming

* Adroit Business Solutions, Inc
* Zaawansowane skojarzenia zabezpieczeń Digital Broadcast
* Kablosuz Iletism Sanayive dis Ticaret A.S.
* Technologie Albis Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* Oprogramowanie multimedialne z AVC co., Ltd.
* Cavium, Inc.
* EchoStar zakup firmy
* Enseo, Inc.
* Fluendo sa
* HANDAN BroadInfoCom co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA sa 
* Wolność usługi globalne usług BV
* MediaTek Inc.
* MStar co, Ltd
* Nintendo co., Ltd.
* OpenTV, Inc.
* Saffron cyfrowo
* Sichuan Changhong, co., Ltd
* SoftAtHome
* Firma Sony Corporation
* Tatung Technology Inc.
* TCL Technology Electronics (Huizhou) co., Ltd.
* Najlepsze inwestycje Victory, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Ostateczne licencje umowy licencyjnej programu Microsoft Smooth Streaming Client
* Zaawansowane skojarzenia zabezpieczeń Digital Broadcast
* Kablosuz Iletism Sanayive dis Ticaret A.S.
* Technologie Albis Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONIK SIECI SAN. ZAPISZ KÓŁKO I KRZYŻYK. A. Ş
* Emisja brytyjskiej przestrzeni powietrznej
* CastPal Technology Inc., Shenzhen
* Branżowe elektronika, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar zakup firmy
* Enseo, Inc.
* Ograniczone filmy FilmFlex
* Fluendo sa
* FUNAI ELEKTRYCZNY CO., LTD
* Gibson innowacje ograniczone
* Haier informacje S. R. L
* HANDAN BroadInfoCom co., Ltd.
* Hisense International co., Ltd. 
* Homecast co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia co., Ltd.
* KDDI Corporation
* Nintendo co., Ltd.
* Pomarańczowy SA
* Saffron cyfrowo
* Sagemcom szerokopasmowe SAS
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhena elektronika CO., LTD
* Shenzhen Jiuzhou, co., Ltd
* Shenzhen Skyworth — Technologia cyfrowa co., Ltd
* Sichuan Changhong, co., Ltd.
* Skardin przemysł Corp.
* Fernsehen & GmbH Deutschland
* SmarDTV sa
* SoftAtHome
* Firma Sony Corporation
* TCL — Marketing zamorski (SRA Makau) — ograniczone
* Technologie dostarczania Technicolor, SAS
* Tongfang Global Ltd.
* Najlepsze inwestycje Victory, Ltd.
* Produkty firmy Toshiba o stylu życia w & Services Corporation
* Uniwersalne Media Corporation/Slovakia/s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Prześlij opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

