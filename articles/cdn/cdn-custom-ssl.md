---
title: Samouczek — konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN | Microsoft Docs
description: Z tego samouczka dowiesz się, jak włączać i wyłączać protokół HTTPS w domenie niestandardowej punktu końcowego usługi Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 86b20e0f317a14db415feff68b17aa99e1e42cb4
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258443"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Samouczek: konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN

> [!IMPORTANT]
> Ta funkcja nie jest dostępna z produktami **Azure CDN Standard from Akamai**. Aby zobaczyć porównanie funkcji usługi CDN, zobacz [Omówienie usługi Azure CDN](cdn-features.md).

Ten samouczek pokazuje, jak włączyć protokół HTTPS dla domeny niestandardowej, którą skojarzono z punktem końcowym usługi Azure Content Delivery Network (CDN). Dzięki użyciu protokołu HTTPS w domenie niestandardowej (na przykład https:\//www.contoso.com) zyskujesz pewność, że poufne dane są bezpiecznie dostarczane za pośrednictwem szyfrowania SSL, gdy są wysyłane przez Internet. Protokół HTTPS oferuje relację zaufania i uwierzytelnianie, a także chroni aplikacje internetowe przed atakami. 

Domyślnie usługa Azure CDN obsługuje protokół HTTPS w obrębie nazwy hosta punktu końcowego usługi CDN. Na przykład w przypadku tworzenia punktu końcowego usługi CDN (takiego jak https:\//contoso.azureedge.net) protokół HTTPS jest włączany automatycznie.  

Niektóre z kluczowych atrybutów niestandardowej funkcji HTTPS to:

- Brak dodatkowych kosztów: nie ma kosztów nabycia ani odnowienia certyfikatu ani dodatkowego kosztu związanego z ruchem protokołu HTTPS. Płacisz tylko za ruch wychodzący z sieci CDN (GB).

- Proste włączanie: w witrynie [Azure Portal](https://portal.azure.com) jest dostępna aprowizacja przy użyciu jednego kliknięcia. Aby włączyć tę funkcję, można użyć interfejsu API REST lub innych narzędzi dla deweloperów.

- Dostępne jest kompletne zarządzanie certyfikatami: użytkownik obsługuje wszystkie operacje nabywania certyfikatów i zarządzania nimi. Certyfikaty są automatycznie aprowizowane i odnawiane przed wygaśnięciem ważności, co powoduje wyeliminowanie ryzyka przerwania działania usługi z powodu wygaśnięcia ważności certyfikatu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Włączanie protokołu HTTPS w domenie niestandardowej
> - Używanie certyfikatu zarządzanego przez usługę CDN 
> - Używanie własnego certyfikatu
> - Weryfikowanie domeny
> - Wyłączanie protokołu HTTPS w domenie niestandardowej

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć profil usługi CDN i co najmniej jeden punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](cdn-create-new-endpoint.md).

Ponadto należy skojarzyć domenę niestandardową usługi Azure CDN w punkcie końcowym usługi CDN. Aby uzyskać więcej informacji, zobacz [Samouczek: dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md)

---

## <a name="ssl-certificates"></a>Certyfikaty SSL
Aby włączyć funkcję HTTPS w celu bezpiecznego dostarczania zawartości w domenie niestandardowej usługi Azure CDN, należy użyć certyfikatu SSL. Możesz użyć certyfikatu zarządzanego przez usługę Azure CDN lub własnego certyfikatu.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opcja 1 (domyślna): włączanie funkcji HTTPS przy użyciu certyfikatu zarządzanego przez usługę Azure CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

W przypadku korzystania z certyfikatu zarządzanego przez usługę Azure CDN można włączyć funkcję HTTPS za pomocą kilku kliknięć. Usługa Azure CDN w pełni obsługuje zadania zarządzania certyfikatami, takie jak nabywanie i odnawianie. Po włączeniu funkcji proces rozpocznie się automatycznie. Jeśli domena niestandardowa jest już zmapowana na punkt końcowy CDN, żadne dalsze działania nie są wymagane. Usługa Azure CDN przetworzy kroki i wykona żądanie automatycznie. Jeśli jednak domena niestandardowa jest zmapowana w innej lokalizacji, należy użyć widomości e-mail, aby zweryfikować własność domeny.

Aby włączyć protokół HTTPS w domenie niestandardowej, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do profilu usługi **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon**.

2. Na liście punktów końcowych usługi CDN wybierz punkt końcowy zawierający domenę niestandardową.

    ![Lista punktów końcowych](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Zostanie wyświetlona strona **Punkt końcowy**.

3. Na liście domen niestandardowych wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

    ![Lista domen niestandardowych](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Zostanie wyświetlona strona **Domena niestandardowa**.

4. W obszarze typu zarządzania certyfikatem wybierz pozycję **Zarządzany przez usługę CDN**.

5. Wybierz pozycję **Wł.**, aby włączyć protokół HTTPS.

    ![Stan protokołu HTTPS domeny niestandardowej](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. Przejdź do kroku [Weryfikowanie domeny](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[Opcja 2: włączanie funkcji HTTPS przy użyciu własnego certyfikatu](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Ta opcja jest dostępna tylko w ramach profilów **usługi Azure CDN w warstwie Standardowa od firmy Microsoft**. 
>
 
Możesz włączyć funkcję HTTPS przy użyciu własnego certyfikatu. Ten proces odbywa się dzięki integracji z usługą Azure Key Vault, umożliwiającą bezpieczne przechowywanie certyfikatów. Ten bezpieczny mechanizm, używany w usłudze Azure CDN do pobrania certyfikatu, wymaga wykonania kilku dodatkowych czynności.

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Przygotowywanie certyfikatu i konta usługi Azure Key Vault
 
1. Usługa Azure Key Vault: musisz mieć konto usługi Azure Key Vault uruchomione w ramach tej samej subskrypcji co profil usługi Azure CDN i punkty końcowe CDN, dla których chcesz włączyć niestandardowy protokół HTTPS. Utwórz konto usługi Azure Key Vault, jeśli go nie masz.
 
2. Certyfikaty usługi Azure Key Vault: jeśli masz już certyfikat, możesz przekazać go bezpośrednio na konto usługi Azure Key Vault lub utworzyć nowy certyfikat bezpośrednio za pomocą usługi Azure Key Vault za pośrednictwem jednego z partnerskich urzędów certyfikacji, z którymi zintegrowana jest usługa Azure Key Vault. 

### <a name="register-azure-cdn"></a>Rejestrowanie usługi Azure CDN

Rejestrowanie usługi Azure CDN jako aplikacji w usłudze Azure Active Directory za pomocą programu PowerShell.

1. Jeśli to konieczne, zainstaluj program [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM/6.0.0) za pomocą programu PowerShell na komputerze lokalnym.

2. W programie PowerShell uruchom następujące polecenie:

     `New-AzureRmADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Rejestrowanie usługi Azure CDN za pomocą programu PowerShell](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Udzielanie usłudze Azure CDN dostępu do magazynu kluczy
 
Udziel usłudze Azure CDN uprawnień dostępu do certyfikatów (wpisów tajnych) na koncie usługi Azure Key Vault.

1. Na koncie magazynu kluczy w obszarze USTAWIENIA wybierz pozycję **Zasady dostępu**, a następnie wybierz pozycję **Dodaj nową**, aby utworzyć nową zasadę.

    ![Dodawanie nowej zasady dostępu](./media/cdn-custom-ssl/cdn-new-access-policy.png)

    ![Ustawienia zasad dostępu](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

2. W obszarze **Wybierz podmiot zabezpieczeń** wyszukaj, a następnie wybierz pozycję **Azure CDN**.

3. W obszarze **Uprawnienia klucza tajnego** wybierz pozycję **Pobierz**, aby zezwolić usłudze CDN na wykonanie tych uprawnień do pobrania i wyświetlenia certyfikatów. 

4. Kliknij przycisk **OK**. 

    Usługa Azure CDN ma teraz dostęp do tego magazynu kluczy i certyfikatów (kluczy tajnych), które są przechowywane w tym magazynie kluczy.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Wybieranie certyfikatu do wdrożenia dla usługi Azure CDN
 
1. Wróć do portalu usługi Azure CDN, a następnie wybierz profil i punkt końcowy CDN, dla którego chcesz włączyć niestandardowy protokół HTTPS. 

2. Na liście domen niestandardowych wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

    Zostanie wyświetlona strona **Domena niestandardowa**.

3. W obszarze Typ zarządzania certyfikatami wybierz pozycję **Użyj własnego certyfikatu**. 

    ![Konfigurowanie własnego certyfikatu](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Wybierz magazyn kluczy, certyfikat (klucz tajny) i wersję certyfikatu.

    Usługa Azure CDN wyświetli następujące informacje: 
    - Konta magazynów kluczy dla Twojego identyfikatora subskrypcji. 
    - Certyfikaty (klucze tajne) dla wybranego magazynu kluczy. 
    - Dostępne wersje certyfikatów. 
 
5. Wybierz pozycję **Wł.**, aby włączyć protokół HTTPS.
  
6. Jeśli używasz własnego certyfikatu, walidacja domeny nie jest wymagana. Przejdź do sekcji [Oczekiwanie na propagację](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Weryfikowanie domeny

Jeśli używasz już domeny niestandardowej mapowanej na niestandardowy punkt końcowy z rekordu CNAME lub własnego certyfikatu, przejdź do sekcji  
[Domena niestandardowa została zamapowana do punktu końcowego usługi CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). W przeciwnym razie, jeśli wpis rekordu CNAME dla punktu końcowego już nie istnieje lub zawiera domenę podrzędną cdnverify, przejdź do sekcji [Domena niestandardowa nie została zamapowana na punkt końcowy usługi CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Domena niestandardowa została zamapowana na punkt końcowy usługi CDN według rekordu CNAME

Po dodaniu domeny niestandardowej do punktu końcowego utworzono rekord CNAME w tabeli DNS rejestratora domen w celu zamapowania go na nazwę hosta punktu końcowego usługi CDN. Jeśli ten rekord CNAME nadal istnieje i nie zawiera domeny podrzędnej cdnverify, urząd certyfikacji DigiCert użyje go automatycznie do zweryfikowania prawa własności do domeny niestandardowej. 

Jeśli używasz własnego certyfikatu, walidacja domeny nie jest wymagana.

Rekord CNAME powinien mieć następujący format, gdzie *Nazwa* to nazwa domeny niestandardowej, a *Wartość* to nazwa hosta punktu końcowego usługi CDN:

| Name (Nazwa)            | Typ  | Wartość                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz [Tworzenie rekordu DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain#create-the-cname-dns-records).

Jeśli rekord CNAME na nieprawidłowy format, firma DigiCert weryfikuje nazwę domeny niestandardowej i dodaje ją do certyfikatu alternatywnych nazw podmiotów (SAN, Subject Alternative Names). Firma DigitCert nie wysyła weryfikacyjnej wiadomości e-mail i nie trzeba zatwierdzać swojego żądania. Certyfikat jest ważny przez jeden rok i jest automatycznie odnawiany przed wygaśnięciem. Przejdź do sekcji [Oczekiwanie na propagację](#wait-for-propagation). 

Automatyczna walidacja przeważnie trwa kilka minut. Jeśli w ciągu godziny nie zobaczysz zweryfikowanej domeny, otwórz bilet pomocy technicznej.

>[!NOTE]
>Jeśli u swojego dostawcy DNS masz rekord autoryzacji urzędu certyfikacji (CAA, Certificate Authority Authorization), musi on zawierać firmę DigiCert jako prawidłowy urząd certyfikacji. Rekord CAA umożliwia właścicielom domen określanie za pomocą dostawców DNS, które urzędy certyfikacji będą upoważnione do wystawiania certyfikatów dla danej domeny. Jeśli urząd certyfikacji otrzymuje zamówienie na certyfikat dla domeny, która ma rekord CAA, a ten urząd certyfikacji nie jest wymieniony jako autoryzowany wystawca, nie może wystawić certyfikatu dla tej domeny ani domeny podrzędnej. Informacje o zarządzaniu rekordami CAA można znaleźć w temacie [Manage CAA records (Zarządzanie rekordami CAA)](https://support.dnsimple.com/articles/manage-caa-record/). Informacje o narzędziu obsługi rekordów CAA można znaleźć w temacie [CAA Record Helper (Pomocnik rekordów CAA)](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Domena niestandardowa nie została zamapowana do punktu końcowego usługi CDN

Jeśli wpis rekordu CNAME dla punktu końcowego już nie istnieje lub zawiera domenę podrzędną cdnverify, postępuj zgodnie z dalszymi instrukcjami w tym kroku.

Po włączeniu protokołu HTTPS w domenie niestandardowej urząd certyfikacji DigiCert weryfikuje własność domeny, kontaktując się z jej rejestratorem zgodnie z informacjami o rejestratorze [WHOIS](http://whois.domaintools.com/) domeny. Kontakt jest nawiązywany za pośrednictwem adresu e-mail (domyślnie) lub numeru telefonu wymienionego w rejestracji WHOIS. Zanim protokół HTTPS zostanie uaktywniony w domenie niestandardowej, należy ukończyć walidację domeny. Domenę należy zatwierdzić w ciągu sześciu dni roboczych. Żądania, które nie zostały zatwierdzone w ciągu sześciu dni roboczych, zostaną automatycznie anulowane. 

![Rekord WHOIS](./media/cdn-custom-ssl/whois-record.png)

Firma DigiCert wysyła również weryfikacyjną wiadomość e-mail na dodatkowe adresy e-mail. Jeśli informacje o rejestratorze WHOIS są prywatne, sprawdź, czy możesz zatwierdzić bezpośrednio z jednego z następujących adresów:

admin@&lt;nazwa-domeny.com&gt;  
administrator@&lt;nazwa-domeny.com&gt;  
webmaster@&lt;nazwa-domeny.com&gt;  
hostmaster@&lt;nazwa-domeny.com&gt;  
postmaster@&lt;nazwa-domeny.com&gt;  

W ciągu kilku minut otrzymasz wiadomość e-mail (podobną do poniższego przykładu) z prośbą o zatwierdzenie żądania. Jeśli używasz filtru spamu, dodaj admin@digicert.com do jego listy dozwolonych. Jeśli w ciągu 24 godzin nie otrzymasz wiadomości e-mail, skontaktuj się z działem pomocy technicznej firmy Microsoft.
    
![Wiadomość e-mail dotycząca weryfikacji domeny](./media/cdn-custom-ssl/domain-validation-email.png)

Po kliknięciu linku zatwierdzania następuje przekierowanie do następującego formularza zatwierdzania online: 
    
![Formularz weryfikacji domeny](./media/cdn-custom-ssl/domain-validation-form.png)

Postępuj zgodnie z instrukcjami w formularzu. Dostępne są dwie opcje weryfikacji:

- Możesz zatwierdzać wszystkie przyszłe zamówienia składane za pośrednictwem tego samego konta dla tej samej domeny głównej; na przykład contoso.com. Takie podejście jest zalecane, jeśli zamierzasz dodawać kolejne domeny niestandardowe dla tej samej domeny głównej.

- Możesz zatwierdzać tylko nazwę określonego hosta używaną w tym żądaniu. W przypadku kolejnych żądań będzie wymagane dodatkowe zatwierdzanie.

Po zatwierdzeniu firma DigiCert dodaje nazwę domeny niestandardowej do certyfikatu SAN. Certyfikat jest ważny przez jeden rok i jest automatycznie odnawiany przed wygaśnięciem.

## <a name="wait-for-propagation"></a>Oczekiwanie na propagację

Po zweryfikowaniu nazwy domeny aktywowanie funkcji protokołu HTTPS domeny niestandardowej może potrwać 6–8 godzin. Po ukończeniu procesu stan niestandardowego protokołu HTTPS w witrynie Azure Portal jest ustawiony na **Włączono**, a cztery kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa jest teraz gotowa do korzystania z protokołu HTTPS.

![Okno dialogowe włączania protokołu HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji w przypadku włączania protokołu HTTPS. Po włączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej są wyświetlane cztery kroki operacji. W miarę uaktywniania kolejnych kroków pod każdym wykonywanym aktualnie krokiem są wyświetlane szczegóły dodatkowego kroku podrzędnego. Nie wszystkie kroki podrzędne zostaną wykonane. Po pomyślnym ukończeniu kroku obok niego zostanie wyświetlony zielony znacznik wyboru. 

| Krok operacji | Szczegóły kroku podrzędnego operacji | 
| --- | --- |
| 1 Przesyłanie żądania | Przesyłanie żądania |
| | Trwa przesyłanie żądania HTTPS. |
| | Żądanie HTTPS zostało pomyślnie przesłane. |
| 2 Walidacja domeny | Domena jest automatycznie weryfikowana, jeśli rekord CNAME został zamapowany na punkt końcowy usługi CDN. W przeciwnym razie żądanie weryfikacji zostanie wysłane na adres e-mail z listy w rekordzie rejestracji domeny (rejestrator WHOIS). Jak najszybciej zweryfikuj domenę. |
| | Własność domeny została pomyślnie zweryfikowana. |
| | Żądanie weryfikacji własności domeny wygasło (prawdopodobnie klient nie odpowiedział w ciągu 6 dni). Protokół HTTPS nie zostanie włączony w domenie. * |
| | Żądanie weryfikacji własności klienta zostało odrzucone przez klienta. Protokół HTTPS nie zostanie włączony w domenie. * |
| 3 Aprowizowanie certyfikatu | Trwa wystawianie przez urząd certyfikacji certyfikatu wymaganego do włączenia obsługi protokołu HTTPS w domenie. |
| | Certyfikat został wystawiony i trwa jego wdrażanie w sieci CDN. Może to potrwać do 6 godzin. |
| | Certyfikat został pomyślnie wdrożony w sieci CDN. |
| 4 Ukończenie | Obsługa protokołu HTTPS została pomyślnie włączona w domenie. |

\* Ten komunikat jest wyświetlany tylko w przypadku wystąpienia błędu. 

Jeśli przed przesłaniem żądania wystąpi błąd, zostanie wyświetlony następujący komunikat o błędzie:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Oczyszczanie zasobów — wyłączanie protokołu HTTPS

W poprzednich krokach protokół HTTPS został włączony w domenie niestandardowej. Jeśli nie chcesz już używać domeny niestandardowej z protokołem HTTPS, możesz wyłączyć protokół HTTPS, wykonując poniższe kroki:

### <a name="disable-the-https-feature"></a>Wyłączanie funkcji protokołu HTTPS 

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do profilu usługi **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon**.

2. Na liście punktów końcowych kliknij punkt końcowy zawierający domenę niestandardową.

3. Kliknij domenę niestandardową, w której chcesz wyłączyć protokół HTTPS.

    ![Lista domen niestandardowych](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Kliknij pozycję **Wył.**, aby wyłączyć protokół HTTPS, a następnie kliknij pozycję **Zastosuj**.

    ![Okno niestandardowego protokołu HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Oczekiwanie na propagację

Po wyłączeniu funkcji protokołu HTTPS w domenie niestandardowej wprowadzenie tej zmiany może potrwać 6–8 godzin. Po ukończeniu procesu stan niestandardowego protokołu HTTPS w witrynie Azure Portal jest ustawiony na **Wyłączono**, a trzy kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa nie może już używać protokołu HTTPS.

![Okno dialogowe wyłączania protokołu HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji w przypadku wyłączenia protokołu HTTPS. Po wyłączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej są wyświetlane trzy kroki operacji. W miarę uaktywniania kolejnych kroków pod każdym krokiem są wyświetlane dodatkowe szczegóły. Po pomyślnym ukończeniu kroku obok niego zostanie wyświetlony zielony znacznik wyboru. 

| Postęp operacji | Szczegóły operacji | 
| --- | --- |
| 1 Przesyłanie żądania | Przesyłanie żądania |
| 2 Anulowanie aprowizacji certyfikatu | Usuwanie certyfikatu |
| 3 Kończenie | Certyfikat został usunięty |

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. *Kto jest dostawcą certyfikatów i jaki typ certyfikatu jest używany?*

    W przypadku usługi **Azure CDN from Verizon** używany jest certyfikat alternatywnych nazw podmiotów (SAN) firmy DigiCert. Jeden certyfikat SAN może zabezpieczać wiele w pełni kwalifikowanych nazw domen. W przypadku usługi **Azure CDN Standard from Microsoft** używany jest pojedynczy certyfikat firmy DigiCert.

2. Używasz protokołu TLS/SSL SNI, czy opartego na protokole IP?

    **Usługa Azure CDN from Verizon** używa protokołu TLS/SSL opartego na protokole IP. **Usługa Azure CDN Standard from Microsoft** używała protokołu TLS/SSL SNI.

3. *Co zrobić, jeśli nie otrzymam wiadomości e-mail weryfikującej domenę od firmy DigiCert?*

    Jeśli masz wpis CNAME dla domeny niestandardowej, który wskazuje bezpośrednio na nazwę hosta punktu końcowego (i nie używasz nazwy domeny podrzędnej cdnverify), nie otrzymasz wiadomości e-mail weryfikującej domenę. Walidacja będzie wykonywana automatycznie. W przeciwnym razie, jeśli nie masz wpisu CNAME i nie otrzymasz wiadomości e-mail w ciągu 24 godzin, skontaktuj się z działem pomocy technicznej firmy Microsoft.

4. *Czy używanie certyfikatu SAN jest mniejsze bezpieczne niż certyfikatu dedykowanego?*
    
    Certyfikat SAN działa zgodnie z tymi samymi standardami szyfrowania i zabezpieczeń, co certyfikat dedykowany. Wszystkie wystawiane certyfikaty SSL używają algorytmu SHA-256, który pozwala na stosowanie rozszerzonych zabezpieczeń serwerów.

5. *Czy mogę używać protokołu HTTPS domeny niestandardowej z usługą Azure CDN from Akamai?*

    Ta funkcja nie jest obecnie dostępna dla profili usługi **Azure CDN Standard from Akamai**. Firma Microsoft pracuje nad wprowadzeniem obsługi tej funkcji w najbliższych miesiącach.

6. *Czy muszę mieć rekord autoryzacji urzędu certyfikacji z moim dostawcą DNS?*

    Nie, rekord autoryzacji urzędu certyfikacji nie jest obecnie wymagany. Jeśli jednak istnieje, musi zawierać firmę DigiCert jako prawidłowy urząd certyfikacji.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Włączanie protokołu HTTPS w domenie niestandardowej
> - Używanie certyfikatu zarządzanego przez usługę CDN 
> - Używanie własnego certyfikatu
> - Weryfikowanie domeny
> - Wyłączanie protokołu HTTPS w domenie niestandardowej

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować buforowanie w punkcie końcowym usługi CDN.

> [!div class="nextstepaction"]
> [Samouczek: ustawianie reguł buforowania usługi Azure CDN](cdn-caching-rules-tutorial.md)

