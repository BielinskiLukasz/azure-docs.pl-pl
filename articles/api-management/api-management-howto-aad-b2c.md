---
title: Autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory B2C — usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak autoryzować użytkowników za pomocą usługi Azure Active Directory B2C w usłudze API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: fbba1d9b4bdf1536ed596e9a78e53116fe824027
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232923"
---
> [!WARNING]
> Integracja z usługą Azure Active Directory B2C jest dostępna w [Developer, Standard i Premium](https://azure.microsoft.com/pricing/details/api-management/) tylko warstwy.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Jak autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory B2C w usłudze Azure API Management
## <a name="overview"></a>Przegląd
Usługa Azure Active Directory B2C to rozwiązanie zarządzania tożsamością w chmurze dla aplikacji przeznaczonych dla klientów internetowych i mobilnych. Można go użyć do zarządzania dostępem do portalu dla deweloperów. W tym przewodniku przedstawiono konfigurację, która jest wymagana w usłudze API Management, aby zintegrować z usługą Azure Active Directory B2C. Aby uzyskać informacje na temat włączania dostępu do portalu dla deweloperów przy użyciu klasycznej usługi Azure Active Directory, zobacz [Jak autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory].

> [!NOTE]
> Aby wykonać kroki opisane w tym przewodniku, najpierw musisz mieć dzierżawę usługi Azure Active Directory B2C do tworzenia aplikacji w programie. Ponadto musisz mieć Rejestracja i logowanie za pomocą zasad gotowe. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory B2C

1. Aby rozpocząć, zaloguj się do [witryny Azure portal](https://portal.azure.com) i Znajdź wystąpienie usługi API Management.

   > [!NOTE]
   > Jeśli jeszcze nie utworzono wystąpienia usługi API Management, zobacz [Tworzenie wystąpienia usługi API Management] [ Create an API Management service instance] w [Rozpoczynanie pracy z samouczkiem usługi Azure API Management] [Get started with Azure API Management].

2. W obszarze **zabezpieczeń**, wybierz opcję **tożsamości**. Kliknij przycisk **+ Dodaj** u góry.

   **Dostawcy tożsamości Dodaj** zostanie wyświetlone okienko po prawej stronie. Wybierz **usługi Azure Active Directory B2C**.
    
   ![Dodawanie usługi AAD B2C jako dostawcy tożsamości][api-management-howto-add-b2c-identity-provider]

3. Kopiuj **adres URL przekierowania**.

  ![Adres URL przekierowania dostawcy tożsamości usługi AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. W nowej karcie dostęp do dzierżawy usługi Azure Active Directory B2C w witrynie Azure portal i Otwórz **aplikacje** bloku.

  ![Zarejestruj nową aplikację 1][api-management-howto-aad-b2c-portal-menu]

5. Kliknij przycisk **Dodaj** przycisk, aby utworzyć nową aplikację usługi Azure Active Directory B2C.

  ![Zarejestruj nową aplikację 2][api-management-howto-aad-b2c-add-button]

6. W **nową aplikację** bloku, wprowadź nazwę aplikacji. Wybierz **tak** w obszarze **interfejsu API sieci Web i aplikacji sieci Web**i wybierz polecenie **tak** w obszarze **Zezwalaj na niejawny przepływ**. Następnie wklej **adresu URL przekierowania** skopiowany w kroku 3 w **adres URL odpowiedzi** pola tekstowego.

  ![Rejestrowanie nowej aplikacji 3][api-management-howto-aad-b2c-app-details]

7. Kliknij przycisk **Utwórz**. Po utworzeniu aplikacji zostanie on wyświetlony w **aplikacje** bloku. Kliknij nazwę aplikacji, aby wyświetlić jego szczegóły.

  ![Zarejestruj nową aplikację 4][api-management-howto-aad-b2c-app-created]

8. Z **właściwości** bloku, kopia **identyfikator aplikacji** do Schowka.

  ![Identyfikator aplikacji 1][api-management-howto-aad-b2c-app-id]

9. Przejdź z powrotem do usługi API Management **dostawcy tożsamości Dodaj** okienka i wklej identyfikator do **identyfikator klienta** pola tekstowego.

  ![Identyfikator aplikacji 2][api-management-howto-aad-b2c-client-id]

10. Przejdź z powrotem do rejestracji aplikacji B2C, kliknij przycisk **klucze** przycisk, a następnie kliknij przycisk **Wygeneruj klucz**. Kliknij przycisk **Zapisz** Aby zapisać konfigurację i wyświetlić **klucz aplikacji**. Skopiuj klucz do Schowka.

  ![Klucz aplikacji 1][api-management-howto-aad-b2c-app-key]

11. Przejdź z powrotem do usługi API Management **dostawcy tożsamości Dodaj** okienka i Wklej klucz do **klucz tajny klienta** pola tekstowego.

  ![Klucz aplikacji 2][api-management-howto-aad-b2c-client-secret]

12. Określ nazwę domeny dzierżawy usługi Azure Active Directory B2C w **dozwolone dzierżawy**.

  ![Dozwolona dzierżawa][api-management-howto-aad-b2c-allowed-tenant]

13. Określ **zasad rejestracji** i **logowanie za pomocą zasad** zasad dzierżawy B2C. Opcjonalnie możesz też podać **zasady edytowania profilu** i **zasady resetowania hasła**.

  ![Zasady][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Aby uzyskać więcej informacji na temat zasad, zobacz [Usługa Azure Active Directory B2C: Rozszerzalna struktura zasad].

14. Po określeniu odpowiednią konfigurację, kliknij przycisk **Zapisz**.

  Po zapisaniu zmian deweloperzy będą mogli tworzyć nowych kont i zaloguj się do portalu dla deweloperów przy użyciu usługi Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Załóż konto dewelopera przy użyciu usługi Azure Active Directory B2C

1. Załóż konto dewelopera przy użyciu usługi Azure Active Directory B2C, Otwórz nowe okno przeglądarki i przejdź do portalu dla deweloperów. Kliknij przycisk **Zarejestruj** przycisku.

   ![Portal dla deweloperów 1][api-management-howto-aad-b2c-dev-portal]

2. Wybierz zarejestrować się przy użyciu **usługi Azure Active Directory B2C**.

   ![Portal dla deweloperów 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Użytkownik jest przekierowany do zasad rejestracji, który został skonfigurowany w poprzedniej sekcji. Wybierz zarejestrować się przy użyciu adresu e-mail lub jeden z istniejących kont społecznościowych.

   > [!NOTE]
   > Jeśli usługi Azure Active Directory B2C jest jedyną opcją, który został włączony na **tożsamości** karta w portalu wydawcy, nastąpi przekierowanie do zasad rejestracji bezpośrednio.

   ![Portal dla deweloperów][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Po zakończeniu rejestracji są przekierowywane do portalu dla deweloperów. Możesz teraz zalogowany do portalu dla deweloperów dla swojego wystąpienia usługi API Management.

    ![Rejestracja zakończona][api-management-registration-complete]

## <a name="next-steps"></a>Kolejne kroki

*  [Omówienie usługi Azure Active Directory B2C]
*  [Usługa Azure Active Directory B2C: Rozszerzalna struktura zasad]
*  [Korzystanie z konta Microsoft jako dostawcy tożsamości w usłudze Azure Active Directory B2C]
*  [Użyj konta Google jako dostawcy tożsamości w usłudze Azure Active Directory B2C]
*  [Użyj konta LinkedIn jako dostawcy tożsamości w usłudze Azure Active Directory B2C]
*  [Użyj konta usługi Facebook jako dostawcy tożsamości w usłudze Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Omówienie usługi Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Jak autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Usługa Azure Active Directory B2C: Rozszerzalna struktura zasad]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Korzystanie z konta Microsoft jako dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Użyj konta Google jako dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Użyj konta usługi Facebook jako dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Użyj konta LinkedIn jako dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
