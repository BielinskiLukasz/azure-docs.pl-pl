---
title: Jak zabezpieczyć urządzenie z ograniczeniami wejściowymi przy użyciu usługi Azure AD i interfejsów API REST Azure Maps
titleSuffix: Azure Maps
description: Jak skonfigurować aplikację bez przeglądarki, która obsługuje logowanie do usługi Azure AD i wywołuje Azure Maps interfejsów API REST.
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b9e75a25237a76c7fa6d300e01bb4d42db75fe08
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988630"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Zabezpieczanie urządzenia wejściowego z ograniczeniami przy użyciu usługi Azure AD i interfejsów API REST Azure Maps

W tym przewodniku omówiono sposób zabezpieczania publicznych aplikacji lub urządzeń, które nie mogą bezpiecznie przechowywać wpisów tajnych ani akceptować danych wejściowych przeglądarki. Te typy aplikacji zaliczają się do kategorii rzeczy lub Internetu rzeczy. Przykładami tych aplikacji mogą być: inteligentne urządzenia TV lub dane czujników emitujące aplikacje. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Tworzenie rejestracji aplikacji w usłudze Azure AD

> [!NOTE]
> * **Odczytywanie wymagań wstępnych:** [Scenariusz: aplikacja klasyczna, która wywołuje interfejsy API sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-overview)
> * W poniższym scenariuszu jest używany przepływ kodu urządzenia, który nie obejmuje przeglądarki sieci Web w celu uzyskania tokenu.

Utwórz aplikację opartą na urządzeniach w usłudze Azure AD, aby włączyć logowanie za pomocą usługi Azure AD. Ta aplikacja uzyska dostęp do Azure Maps interfejsów API REST.

1. W Azure Portal na liście usług platformy Azure wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**.  

    > [!div class="mx-imgBorder"]
    > ![Rejestrowanie aplikacji](./media/how-to-manage-authentication/app-registration.png)

2. Wprowadź **nazwę**, wybierz **konta w tym katalogu organizacji tylko** jako **obsługiwany typ konta**. W obszarze **identyfikatory URI przekierowania**określ opcję **Klient publiczny/natywny (Mobile & Desktop)** , a następnie Dodaj `https://login.microsoftonline.com/common/oauth2/nativeclient` do wartości. Aby uzyskać więcej informacji [, zobacz Aplikacja klasyczna usługi Azure AD, która wywołuje interfejsy API sieci Web: rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration). Następnie **zarejestruj** aplikację.

    > [!div class="mx-imgBorder"]
    > ![Dodaj szczegóły rejestracji aplikacji dla nazwy i identyfikatora URI przekierowania](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Przejdź do opcji **uwierzytelnianie** i Włącz **aplikację Traktuj jako klienta publicznego**. Spowoduje to włączenie uwierzytelniania kodu urządzenia w usłudze Azure AD.
    
    > [!div class="mx-imgBorder"]
    > ![Włącz rejestrację aplikacji jako klienta publicznego](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Aby przypisać delegowane uprawnienia interfejsu API do Azure Maps, przejdź do aplikacji. Następnie wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**. W obszarze interfejsy API, które są **wykorzystywane przez moją organizację**, Wyszukaj i wybierz **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![Dodawanie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

5. Zaznacz pole wyboru obok pozycji **dostęp Azure Maps**, a następnie wybierz pozycję **Dodaj uprawnienia**.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

6. Konfigurowanie kontroli dostępu opartej na rolach na platformie Azure dla użytkowników lub grup. Zobacz [udzielanie użytkownikom dostępu do Azure Maps](#grant-role-based-access-for-users-to-azure-maps).

7. Dodaj kod w celu uzyskania przepływu tokenu w aplikacji, aby poznać szczegóły implementacji, zobacz [przepływ kodu urządzenia](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-acquire-token#device-code-flow). Podczas uzyskiwania tokenów odwołują się do zakresu: `user_impersonation` który został wybrany w poprzednich krokach.

> [!Tip]
> Użyj biblioteki Microsoft Authentication Library (MSAL), aby uzyskać tokeny dostępu. Zobacz zalecenia dotyczące [aplikacji klasycznej, która wywołuje interfejsy API sieci Web: Konfiguracja kodu](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-configuration)

8. Utwórz żądanie HTTP przy użyciu uzyskanego tokenu z usługi Azure AD i Wyślij żądanie z prawidłowym klientem HTTP.

### <a name="sample-request"></a>Przykładowe żądanie
Oto przykładowa treść żądania do przekazywania prostego geoogrodzenia reprezentowanego jako okrąg geometryczny przy użyciu punktu centralnego i promienia.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 Treść przykładowego żądania znajduje się w GeoJSON:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Przykładowa odpowiedź:

Nagłówka
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Jednostce
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Następne kroki

Znajdź metryki użycia interfejsu API dla konta usługi Azure Maps:
> [!div class="nextstepaction"]
> [Wyświetlanie metryk użycia](how-to-view-api-usage.md)
