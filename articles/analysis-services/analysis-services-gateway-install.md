---
title: Zainstaluj lokalną bramę danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować lokalną bramę danych.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/07/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 89b21af5303afc2082d3d56ddb9e894f3ae4c4b8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158433"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalowanie i konfigurowanie bramy danych lokalnych
Lokalna brama danych jest wymagana, gdy co najmniej jeden serwer usług Azure Analysis Services, w tym samym regionie, łączenie ze źródłami danych w środowisku lokalnym. Aby dowiedzieć się więcej na temat bramy, zobacz [lokalnej bramy danych](analysis-services-gateway.md).

## <a name="prerequisites"></a>Wymagania wstępne
**Minimalne wymagania:**

* .NET 4.5 framework
* 64-bitowej wersji systemu Windows 7 / Windows Server 2008 R2 (lub nowszy)

**Zalecane:**

* 8 rdzeni procesora CPU
* 8 GB pamięci RAM
* 64-bitowej wersji systemu Windows 2012 R2 (lub nowszym)

**Ważne uwagi:**

* Podczas instalacji, podczas rejestracji bramy przy użyciu platformy Azure wybrano domyślnego regionu dla Twojej subskrypcji. Możesz wybrać inny region. W przypadku serwerów w więcej niż jednym regionie, należy zainstalować bramę dla każdego regionu. 
* Nie można zainstalować bramy na kontrolerze domeny.
* Na jednym komputerze można zainstalować tylko jedną bramę.
* Domyślnie brama używa konta NT SERVICE\PBIEgwService logowania. Inne konto można określić podczas instalacji lub w usługach. Upewnij się, że zasady grupy Zezwalaj na konto usługi ma dziennika jako uprawnienia usługi.
* Instalowanie bramy na komputerze, który pozostaje na, a nie przechodzi w stan uśpienia.
* Nie należy instalować bramy na komputerze bezprzewodowo podłączonej do sieci. Wydajność może być mniejsza.
* Logowanie do platformy Azure przy użyciu konta w usłudze Azure AD dla tej samej [dzierżawy](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) co subskrypcji w przypadku rejestracji bramy w. Funkcje B2B platformy Azure (Gość) konta nie są obsługiwane, podczas instalowania i rejestrowania bramy.
* W przypadku źródeł danych znajdują się na usługi Azure Virtual Network (VNet), należy skonfigurować [AlwaysUseGateway](analysis-services-vnet-gateway.md) właściwości serwera.
* (Ujednoliconej) bramy, które są opisane w tym miejscu nie jest obsługiwana w niezależne regiony platformy Azure Government, Azure (Niemcy) i Azure (Chiny). Użyj **dedykowanych lokalnej bramy dla usług Azure Analysis Services**zainstalowanego na serwerze server **— Szybki Start** w portalu. 


## <a name="download"></a>Pobierz
 [Pobierz bramę](https://aka.ms/azureasgateway)

## <a name="install"></a>Zainstaluj

1. Uruchom Instalatora.

2. Wybierz lokalizację, zaakceptuj warunki, a następnie kliknij **zainstalować**.

   ![Zainstaluj lokalizacji oraz postanowienia licencyjne](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Zaloguj się do platformy Azure. Konto musi być dzierżawy usługi Azure Active Directory. To konto jest używane dla administratora bramy. Funkcje B2B platformy Azure (Gość) konta nie są obsługiwane, podczas instalowania i rejestrowania bramy.

   ![Logowanie do platformy Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Jeśli zalogujesz się przy użyciu konta domeny w usłudze Azure AD jest mapowany do swojego konta organizacyjnego. Konta organizacji jest używane jako administrator bramy.

## <a name="register"></a>Zarejestruj się
W celu utworzenia zasobu bramy na platformie Azure, możesz zarejestrować lokalne wystąpienie, który został zainstalowany przy użyciu usługi bramy w chmurze. 

1.  Wybierz **Zarejestruj nową bramę na tym komputerze**.

    ![Zarejestruj subskrypcję](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Wpisz nazwę i klucz odzyskiwania dla bramy. Domyślnie Twoja subskrypcja domyślnego regionu używa brama. Jeśli musisz wybrać inny region, wybierz opcję **Zmień Region**.

    > [!IMPORTANT]
    > Klucz odzyskiwania należy zapisać w bezpiecznym miejscu. Klucz odzyskiwania jest wymagany w prawidłowej kolejności do przejęcia, migracji lub przywracania bramy. 

   ![Zarejestruj subskrypcję](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Tworzenie zasobu brama platformy Azure
Po został zainstalowany i zarejestrowany bramy, należy do utworzenia zasobu bramy w Twojej subskrypcji platformy Azure. Zaloguj się do platformy Azure przy użyciu tego samego konta, którego użyto podczas rejestracji bramy.

1. W witrynie Azure portal kliknij **Utwórz nową usługę** > **integracji dla przedsiębiorstw** > **lokalnej bramy danych**  >   **Utwórz**.

   ![Utwórz zasób bramy](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. W **Utwórz bramę połączenia**, wprowadź następujące ustawienia:

    * **Nazwa**: Wprowadź nazwę zasobu bramy. 

    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, aby skojarzyć z zasobem usługi bramy. 
   
      Domyślna subskrypcja opiera się na konto platformy Azure, która użyte do zalogowania.

    * **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą.

    * **Lokalizacja**: Wybierz region, zarejestrować bramę.

    * **Nazwa instalacji**: Jeśli Twoja instalacja bramy nie została jeszcze wybrana, wybierz bramy, zarejestrowane. 

    Gdy wszystko będzie gotowe, kliknij przycisk **Utwórz**.

## <a name="connect-servers"></a>Połącz serwery do zasobu bramy

1. W sieci — Omówienie serwera usług Azure Analysis Services kliknij **lokalnej bramy danych**.

   ![Połączyć serwer bramy](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. W **wybierz lokalną bramę danych do łączenia z**wybierz zasób bramy, a następnie kliknij przycisk **Połącz wybraną bramę**.

   ![Połącz serwer do zasobu bramy](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Jeśli na liście nie ma bramy, serwer, prawdopodobnie nie w tym samym regionie, co region określone podczas rejestracji bramy. 

Gotowe. Jeśli potrzebujesz otwarcie portów lub wykonaj rozwiązywania wszelkich problemów, koniecznie zapoznaj się z [lokalnej bramy danych](analysis-services-gateway.md).

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie usług Analysis Services](analysis-services-manage.md)   
* [Pobieranie danych z usług Azure Analysis Services](analysis-services-connect.md)   
* [Używanie bramy dla źródeł danych w usłudze Azure Virtual Network](analysis-services-vnet-gateway.md)
