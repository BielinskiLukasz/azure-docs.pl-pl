---
title: Autoryzuj konta dewelopera przy użyciu Azure Active Directory B2C API Management platformy Azure | Microsoft Docs
description: Dowiedz się, jak autoryzować użytkowników przy użyciu Azure Active Directory B2C w API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 55a9cbcfdb9766c6adc6d10f975fa4d074ec3aaf
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011663"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Jak autoryzować konta dewelopera przy użyciu Azure Active Directory B2C na platformie Azure API Management

## <a name="overview"></a>Omówienie

Usługa Azure Active Directory B2C to rozwiązanie zarządzania tożsamością w chmurze dla aplikacji przeznaczonych dla klientów internetowych i mobilnych. Służy do zarządzania dostępem do portalu dla deweloperów. W tym przewodniku przedstawiono konfigurację, która jest wymagana w ramach usługi API Management do integracji z Azure Active Directory B2C. Aby uzyskać informacje na temat włączania dostępu do portalu dla deweloperów przy użyciu klasycznej Azure Active Directory, zobacz [jak autoryzować konta dewelopera przy użyciu Azure Active Directory].

> [!NOTE]
> Aby wykonać kroki opisane w tym przewodniku, musisz najpierw mieć dzierżawę Azure Active Directory B2C, aby utworzyć aplikację w programie. Ponadto musisz mieć gotowe zasady rejestracji i logowania. Aby uzyskać więcej informacji, zobacz [Przegląd Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autoryzuj konta dewelopera przy użyciu Azure Active Directory B2C

1. Aby rozpocząć, zaloguj się do [Azure Portal](https://portal.azure.com) i Znajdź swoje wystąpienie API Management.

   > [!NOTE]
   > Jeśli nie utworzono jeszcze wystąpienia usługi API Management, zobacz [Tworzenie wystąpienia usługi API Management][Create an API Management service instance] w samouczku Rozpoczynanie [pracy z platformą Azure API Management][Get started with Azure API Management].

2. W obszarze **tożsamości**. Kliknij pozycję **+ Dodaj** u góry.

   Po prawej stronie pojawi się okienko **Dodawanie dostawcy tożsamości** . Wybierz **Azure Active Directory B2C**.
    
   ![Dodaj AAD B2C jako dostawcę tożsamości][api-management-howto-add-b2c-identity-provider]

3. Skopiuj **adres URL przekierowania**.

   ![Adres URL przekierowania dostawcy tożsamości AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. Na nowej karcie uzyskaj dostęp do dzierżawy Azure Active Directory B2C w Azure Portal i Otwórz blok **aplikacje** .

   ![Rejestrowanie nowej aplikacji 1][api-management-howto-aad-b2c-portal-menu]

5. Kliknij przycisk **Dodaj** , aby utworzyć nową aplikację Azure Active Directory B2C.

   ![Rejestrowanie nowej aplikacji 2][api-management-howto-aad-b2c-add-button]

6. W bloku **Nowa aplikacja** wprowadź nazwę aplikacji. Wybierz pozycję **tak** w obszarze **aplikacja sieci Web/Web API**, a następnie wybierz pozycję **tak** w obszarze **Zezwalaj na niejawny przepływ**. Następnie wklej **adres URL przekierowania** skopiowany w kroku 3 do pola tekstowego **adres URL odpowiedzi** .

   ![Rejestrowanie nowej aplikacji 3][api-management-howto-aad-b2c-app-details]

7. Kliknij przycisk **Utwórz**. Gdy aplikacja zostanie utworzona, zostanie wyświetlona w bloku **aplikacje** . Kliknij nazwę aplikacji, aby wyświetlić jej szczegóły.

   ![Rejestrowanie nowej aplikacji 4][api-management-howto-aad-b2c-app-created]

8. W bloku **Właściwości** Skopiuj **Identyfikator aplikacji** do Schowka.

   ![Identyfikator aplikacji 1][api-management-howto-aad-b2c-app-id]

9. Przełącz się z powrotem do API Management **Dodaj dostawcę tożsamości** i wklej identyfikator do pola tekstowego **Identyfikator klienta** .
    
10. Wróć do rejestracji aplikacji B2C, kliknij przycisk **klucze** , a następnie kliknij pozycję **Generuj klucz**. Kliknij przycisk **Zapisz** , aby zapisać konfigurację i wyświetlić **klucz aplikacji**. Skopiuj klucz do Schowka.

    ![Klucz aplikacji 1][api-management-howto-aad-b2c-app-key]

11. Przełącz się z powrotem do API Management **Dodaj dostawcę tożsamości** i Wklej klucz do pola tekstowego **klucz tajny klienta** .
    
12. Określ nazwę domeny dzierżawcy Azure Active Directory B2C w **dzierżawie logowania**.

13. Pole **Urząd** pozwala kontrolować adres URL logowania Azure AD B2C, który ma być używany. Ustaw wartość na **< your_b2c_tenant_name >. b2clogin. com**.

14. Określ zasady **rejestracji i** **zasady logowania** z zasad dzierżawy B2C. Opcjonalnie możesz również podać zasady **edytowania profilu** i **Zasady resetowania hasła**.

15. Po określeniu odpowiedniej konfiguracji kliknij przycisk **Zapisz**.

    Po zapisaniu zmian deweloperzy będą mogli tworzyć nowe konta i zalogować się do portalu dla deweloperów przy użyciu Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portal dla deweloperów — Dodawanie uwierzytelniania Azure AD B2C konta

W portalu dla deweloperów zalogowanie się za pomocą AAD B2C jest możliwe za pomocą widżetu **przyciski uwierzytelniania OAuth** . Element widget jest już uwzględniony na stronie logowania domyślnej zawartości portalu deweloperów.

![Widżet przycisków usługi AAD](./media/api-management-howto-aad/portal-oauth-widget.png)

Mimo że nowe konto zostanie utworzone automatycznie za każdym razem, gdy nowy użytkownik zaloguje się za pomocą AAD B2C, można rozważyć dodanie tego samego widżetu do strony rejestracji.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Starszy Portal dla deweloperów — jak utworzyć konto za pomocą Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Aby utworzyć konto dewelopera przy użyciu Azure Active Directory B2C, Otwórz nowe okno przeglądarki i przejdź do portalu dla deweloperów. Kliknij przycisk **Utwórz konto** .

   ![Portal dla deweloperów 1][api-management-howto-aad-b2c-dev-portal]

2. Wybierz, aby zarejestrować się w usłudze **Azure Active Directory B2C**.

   ![Portal dla deweloperów 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Nastąpi przekierowanie do zasad rejestracji skonfigurowanych w poprzedniej sekcji. Wybierz, aby zarejestrować się przy użyciu swojego adresu e-mail lub jednego z istniejących kont społecznościowych.

   > [!NOTE]
   > Jeśli Azure Active Directory B2C jest jedyną opcją włączoną na karcie **tożsamości** w portalu wydawcy, nastąpi przekierowanie do zasad rejestracji bezpośrednio.

   ![Portal dla deweloperów][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Po zakończeniu rejestracji nastąpi przekierowanie do portalu dla deweloperów. Zalogowano Cię do portalu dla deweloperów dla wystąpienia usługi API Management.

    ![Zakończono rejestrację][api-management-registration-complete]

## <a name="next-steps"></a>Następne kroki

*  [Przegląd Azure Active Directory B2C]
*  [Azure Active Directory B2C: rozszerzalna struktura zasad]
*  [Użyj konto Microsoft jako dostawcy tożsamości w programie Azure Active Directory B2C]
*  [Użyj konta Google jako dostawcy tożsamości w Azure Active Directory B2C]
*  [Używanie konta LinkedIn jako dostawcy tożsamości w Azure Active Directory B2C]
*  [Używanie konta w usłudze Facebook jako dostawcy tożsamości w Azure Active Directory B2C]



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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Przegląd Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Jak autoryzować konta dewelopera przy użyciu Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: rozszerzalna struktura zasad]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Użyj konto Microsoft jako dostawcy tożsamości w programie Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Użyj konta Google jako dostawcy tożsamości w Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Używanie konta w usłudze Facebook jako dostawcy tożsamości w Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Używanie konta LinkedIn jako dostawcy tożsamości w Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
