---
title: Certyfikaty wymagane do zezwalania na serwery zaplecza
titleSuffix: Azure Application Gateway
description: W tym artykule przedstawiono przykłady sposobu konwertowania certyfikatu SSL na certyfikat uwierzytelniania i zaufany certyfikat główny wymagany do zezwalania na wystąpienia zaplecza na platformie Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 48944c513bd075e3859503fdadc4001261dc8c4a
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048170"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Tworzenie certyfikatów w celu zezwalania na zaplecze przy użyciu usługi Azure Application Gateway

Aby wykonać kompleksową obsługę protokołu SSL, Application Gateway wymaga, aby wystąpienia zaplecza były dozwolone przez przekazywanie certyfikatów głównych uwierzytelniania/zaufanych. W przypadku jednostki SKU V1 wymagane są certyfikaty uwierzytelniania, ale dla zaufanych certyfikatów głównych jednostki SKU v2 wymagane jest zezwolenie na certyfikaty.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> - Eksportowanie certyfikatu uwierzytelniania z certyfikatu wewnętrznej bazy danych (dla jednostki SKU v1)
> - Eksportowanie zaufanego certyfikatu głównego z certyfikatu zaplecza (dla jednostki SKU v2)

## <a name="prerequisites"></a>Wymagania wstępne

Istniejący certyfikat zaplecza jest wymagany do wygenerowania certyfikatów uwierzytelniania lub zaufanych certyfikatów głównych wymaganych do zezwalania na wystąpienia zaplecza z Application Gateway. Certyfikat zaplecza może być taki sam jak certyfikat SSL lub inny w celu zwiększenia bezpieczeństwa. Application Gateway nie udostępnia żadnego mechanizmu do tworzenia lub zakupu certyfikatu SSL. Do celów testowych można utworzyć certyfikat z podpisem własnym, ale nie należy go używać do obciążeń produkcyjnych. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Eksportowanie certyfikatu uwierzytelniania (dla jednostki SKU v1)

Certyfikat uwierzytelniania jest wymagany, aby zezwalać na wystąpienia zaplecza w jednostkach SKU Application Gateway v1. Certyfikat uwierzytelniania to klucz publiczny certyfikatów serwera zaplecza w Base-64 zakodowany X. 509 (. CER). W tym przykładzie użyjesz certyfikatu SSL dla certyfikatu zaplecza i wyeksportusz jego klucz publiczny, aby można było go używać jako certyfikatu uwierzytelniania. Ponadto w tym przykładzie za pomocą narzędzia Menedżer certyfikatów systemu Windows można wyeksportować wymagane certyfikaty. Możesz wybrać inne narzędzie, które jest wygodne.

W certyfikacie SSL wyeksportuj plik. cer klucza publicznego (nie klucz prywatny). Poniższe kroki ułatwiają wyeksportowanie pliku CER w formacie X. 509 z kodowaniem Base-64 (. Format CER) dla certyfikatu:

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Znajdź certyfikat, zazwyczaj w "Certificates-Current User\Personal\Certificates" i kliknij prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**. Jeśli nie możesz znaleźć certyfikatu w ramach bieżącego User\Personal\Certificates, być może przypadkowo otwarto "certyfikaty — komputer lokalny", a nie "Certyfikaty — bieżący użytkownik"). Jeśli chcesz otworzyć Menedżera certyfikatów w bieżącym zakresie użytkownika przy użyciu programu PowerShell, wpisz *certmgr* w oknie konsoli.

   ![Eksportowanie](./media/certificates-for-backend-authentication/export.png)

2. W Kreatorze kliknij pozycję **Dalej**.

   ![Eksportowanie certyfikatu](./media/certificates-for-backend-authentication/exportwizard.png)

3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.

   ![Nie Eksportuj klucza prywatnego](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)** , a następnie kliknij pozycję **Dalej**.

   ![Base-64 — zakodowana](./media/certificates-for-backend-authentication/base64.png)

5. W polu **plik do wyeksportowania** **Przejdź** do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![Browse](./media/certificates-for-backend-authentication/browse.png)

6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![Zakończ](./media/certificates-for-backend-authentication/finish.png)

7. Certyfikat został pomyślnie wyeksportowany.

   ![Powodzenie](./media/certificates-for-backend-authentication/success.png)

   Wyeksportowany certyfikat wygląda podobnie do tego:

   ![Możliwy](./media/certificates-for-backend-authentication/exported.png)

8. Jeśli otworzysz wyeksportowany certyfikat przy użyciu Notatnika, zobaczysz coś podobnego do tego przykładu. Sekcja w kolorze niebieskim zawiera informacje przekazane do bramy Application Gateway. Jeśli otworzysz certyfikat przy użyciu programu Notepad i nie jest on podobny do tego, zazwyczaj oznacza to, że nie został on wyeksportowany przy użyciu Base-64 szyfrowanego X. 509 (. CER). Ponadto, jeśli chcesz użyć innego edytora tekstów, należy zrozumieć, że niektóre edytory mogą wprowadzić niezamierzone formatowanie w tle. Może to spowodować problemy podczas przekazywania tekstu z tego certyfikatu do platformy Azure.

   ![Otwórz za pomocą Notatnika](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Eksportuj zaufany certyfikat główny (dla jednostki SKU v2)

Zaufany certyfikat główny jest wymagany do dozwolonych wystąpień zaplecza w jednostce SKU bramy aplikacji w wersji 2. Certyfikat główny to podstawowy-64 zakodowany X. 509 (. CER) format certyfikatu głównego z certyfikatów serwera wewnętrznej bazy danych. W tym przykładzie użyjemy certyfikatu SSL dla certyfikatu zaplecza, Eksportuj swój klucz publiczny, a następnie wyeksportuj certyfikat główny zaufanego urzędu certyfikacji z klucza publicznego w formacie kodowanym algorytmem Base64, aby uzyskać zaufany certyfikat główny. Certyfikaty pośrednie powinny być powiązane z certyfikatem serwera i instalowane na serwerze wewnętrznej bazy danych.

Poniższe kroki ułatwiają wyeksportowanie pliku CER dla certyfikatu:

1. Aby wyeksportować klucz publiczny z certyfikatu zaplecza, należy wykonać kroki 1-9 wymienione w sekcji **Eksportowanie certyfikatu uwierzytelniania z certyfikatu wewnętrznej bazy danych (dla jednostki SKU v1)** .

2. Po wyeksportowaniu klucza publicznego Otwórz plik.

   ![Otwórz certyfikat autoryzacji](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![Informacje o certyfikacie](./media/certificates-for-backend-authentication/general.png)

3. Przejdź do widoku Ścieżka certyfikacji, aby wyświetlić urząd certyfikacji.

   ![Szczegóły certyfikatu](./media/certificates-for-backend-authentication/certdetails.png)

4. Wybierz certyfikat główny, a następnie kliknij pozycję **Wyświetl certyfikat**.

   ![ścieżka certyfikatu](./media/certificates-for-backend-authentication/rootcert.png)

   Powinny pojawić się szczegóły certyfikatu głównego.

   ![Informacje o certyfikatach](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Przejdź do widoku **szczegóły** , a następnie kliknij pozycję **Kopiuj do pliku...**

   ![Kopiuj certyfikat główny](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. W tym momencie wyodrębnisz szczegóły certyfikatu głównego z certyfikatu zaplecza. Zostanie wyświetlony **Kreator eksportu certyfikatów**. Teraz wykonaj kroki 2-9 wymienione w sekcji **Eksportowanie certyfikatu uwierzytelniania z certyfikatu zaplecza (dla jednostki SKU v1)** powyżej, aby wyeksportować zaufany certyfikat główny w formacie X. 509 Base-64. CER).

## <a name="next-steps"></a>Następne kroki

Teraz masz certyfikat uwierzytelniania/zaufany certyfikat główny w Base-64 zakodowany X. 509 (. CER). Można dodać to do bramy aplikacji, aby dozwolonych serwery wewnętrznej bazy danych w celu kompleksowego szyfrowania SSL. Zobacz [jak skonfigurować kompleksowe szyfrowanie SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

