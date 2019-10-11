---
title: Jak używać Twilio do komunikacji głosowej i SMS (Python) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS z usługą interfejsu API Twilio na platformie Azure. Przykłady kodu zapisywane w języku Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242183"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Jak używać Twilio do obsługi głosu i SMS w języku Python
W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne za pomocą usługi interfejsu API Twilio na platformie Azure. Omówione scenariusze obejmują wykonywanie połączeń telefonicznych i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat Twilio i używania programów Voice i SMS w aplikacjach, zobacz sekcję [następne kroki](#NextSteps) .

## <a id="WhatIs"></a>Co to jest Twilio?
Twilio to zaawansowana komunikacja biznesowa, dzięki czemu deweloperzy mogą osadzać głos, VoIP i komunikaty w aplikacjach. Umożliwiają one wirtualizację całej infrastruktury wymaganej w środowisku globalnym opartym na chmurze, dzięki czemu można ją uwidocznić za pośrednictwem platformy interfejsu API komunikacji Twilio. Aplikacje są proste do kompilowania i skalowalności. Korzystaj z elastyczności, aby korzystać z cen z płatność zgodnie z rzeczywistym użyciem, a także skorzystać z niezawodności chmury.

**Twilio Voice** umożliwia aplikacjom tworzenie i odbieranie połączeń telefonicznych.
**TWILIO SMS** umożliwia aplikacji wysyłanie i odbieranie wiadomości SMS.
**Klient Twilio** umożliwia wykonywanie wywołań VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje protokołu WebRTC.

## <a id="Pricing"></a>Ceny Twilio i oferty specjalne
Klienci korzystający z platformy Azure otrzymują [specjalną ofertę][special_offer] $10 kredyt Twilio w przypadku uaktualniania konta Twilio. Ten kredyt Twilio można zastosować do dowolnego Twilioego użycia ($10 kredytu równoważnego do 1 000 wysłania wiadomości SMS lub odebrania do 1000 w ciągu kilku minut, w zależności od lokalizacji numeru telefonu i wiadomości lub miejsca docelowego wywołania). Zrealizuj ten [Twilio kredyt][special_offer] i zacznij pracę.

Twilio to usługa płatność zgodnie z rzeczywistym użyciem. Nie ma żadnych opłat skonfigurowanych i możesz zamknąć konto w dowolnym momencie. Więcej szczegółów można znaleźć w [cenniku Twilio][twilio_pricing].

## <a id="Concepts"></a>Pojęcia
Interfejs API Twilio jest interfejsem API RESTful, który zapewnia funkcje głosu i programu SMS dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [biblioteki interfejsu API Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API Twilio są czasownikami Twilio i Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Zlecenia Twilio
Interfejs API korzysta z czasowników Twilio; na przykład czasownik **&lt;Say @ no__t-2** instruuje Twilio, aby audibly dostarczą komunikat w wywołaniu.

Poniżej znajduje się lista zleceń Twilio. Zapoznaj się z innymi zleceniami i możliwościami za pośrednictwem [dokumentacji języka Twilio Markup][twiml]Language.

* **&lt;Dial @ no__t-2**: łączy obiekt wywołujący z innym numerem telefonu.
* **&lt;Gather @ no__t-2**: zbiera cyfry numeryczne wprowadzone na klawiaturze telefonu.
* **&lt;Hangup @ no__t-2**: przerywa wywołanie.
* **&lt;Pause @ no__t-2**: czeka w trybie cichym przez określoną liczbę sekund.
* **&lt;Play @ no__t-2**: odtwarza plik audio.
* **&lt;Queue @ no__t-2**: Dodaj do kolejki obiektów wywołujących.
* **&lt;Record @ no__t-2**: rejestruje głos obiektu wywołującego i zwraca adres URL pliku, który zawiera nagranie.
* **&lt;Redirect @ no__t-2**: przenosi kontrolę nad wywołaniem lub wiadomością SMS na TwiML pod innym adresem URL.
* **&lt;Reject @ no__t-2**: odrzuca przychodzące wywołanie do numeru Twilio bez rozliczania.
* **&lt;Say @ no__t-2**: konwertuje tekst na mowę, która jest wykonywana w wywołaniu.
* **&lt;Sms @ no__t-2**: wysyła wiadomość SMS.

### <a id="TwiML"></a>TwiML
TwiML to zestaw instrukcji opartych na języku XML opartych na zleceniach Twilio, które informują Twilio o sposobie przetwarzania wywołania lub wiadomości SMS.

Na przykład następujące TwiML spowodują przekonwertowanie tekstu **Hello World** na mowę.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Gdy aplikacja wywołuje interfejs API Twilio, jeden z parametrów interfejsu API jest adresem URL, który zwraca odpowiedź TwiML. W celach programistycznych można użyć adresów URL dostarczonych przez Twilio w celu zapewnienia odpowiedzi TwiML używanych przez aplikacje. Do tworzenia odpowiedzi TwiML można także hostować własne adresy URL, a inna opcja to użycie obiektu `TwiMLResponse`.

Aby uzyskać więcej informacji na temat czasowników Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje o interfejsie API Twilio, zobacz [TWILIO API][twilio_api].

## <a id="CreateAccount"></a>Utwórz konto Twilio
Gdy wszystko będzie gotowe do uzyskania konta Twilio, zarejestruj się na [Wypróbie Twilio][try_twilio]. Możesz zacząć korzystać z bezpłatnego konta i później uaktualnić swoje konto.

Gdy zarejestrujesz się w celu korzystania z konta usługi Twilio, otrzymasz identyfikator SID konta i token uwierzytelniania. Oba te elementy będą konieczne do wykonywania wywołań interfejsu API Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, Zachowaj bezpieczny token uwierzytelniania. Identyfikator SID konta i token uwierzytelniania są widoczne w [konsoli programu Twilio][twilio_console], odpowiednio w polach **identyfikatory SID konta** i **token uwierzytelniania**.

## <a id="create_app"></a>Tworzenie aplikacji w języku Python
Aplikacja języka Python korzystająca z usługi Twilio i działająca na platformie Azure nie jest inna niż jakakolwiek inna aplikacja w języku Python, która korzysta z usługi Twilio. Chociaż usługi Twilio są oparte na architekturze REST i mogą być wywoływane z poziomu języka Python na kilka sposobów, ten artykuł koncentruje się na sposobach używania usług Twilio Services z [biblioteką Twilio dla języka Python z usługi GitHub][twilio_python]. Aby uzyskać więcej informacji o korzystaniu z biblioteki Twilio dla języka Python, zobacz [https://www.twilio.com/docs/libraries/python][twilio_lib_docs].

Najpierw [Skonfiguruj nową maszynę wirtualną platformy Azure z systemem Linux] [azure_vm_setup] do działania jako host dla nowej aplikacji sieci Web w języku Python. Po uruchomieniu maszyny wirtualnej należy uwidocznić aplikację na porcie publicznym, zgodnie z poniższym opisem.

### <a name="add-an-incoming-rule"></a>Dodawanie reguły przychodzącej
  1. Przejdź do strony [Network Security Group] [azure_nsg].
  2. Wybierz grupę zabezpieczeń sieci zgodną z maszyną wirtualną.
  3. Dodaj **regułę i wychodzące reguły** dla **portu 80**. Pamiętaj, aby zezwolić na ruch przychodzący z dowolnego adresu.

### <a name="set-the-dns-name-label"></a>Ustawianie etykiety nazwy DNS
  1. Przejdź do strony [Publiczne adresy IP] [azure_ips].
  2. Wybierz publiczny adres IP, który odnosi się do Twojej maszyny wirtualnej.
  3. W sekcji **Konfiguracja** Ustaw **etykietę nazwa DNS** . W przypadku tego przykładu będzie wyglądać podobnie do tej *etykiety*. centralus.cloudapp.Azure.com

Po nawiązaniu połączenia za pośrednictwem protokołu SSH z maszyną wirtualną można zainstalować dowolnie wybraną platformę sieci Web (dwie najbardziej dobrze znane w języku [Python i](http://flask.pocoo.org/) [Django](https://www.djangoproject.com)). Każdą z nich można zainstalować tylko przez uruchomienie polecenia `pip install`.

Należy pamiętać, że skonfigurowano maszynę wirtualną tak, aby zezwalała na ruch tylko na porcie 80. Upewnij się, że aplikacja ma być skonfigurowana do używania tego portu.

## <a id="configure_app"></a>Konfigurowanie aplikacji do korzystania z bibliotek Twilio
Aplikację można skonfigurować tak, aby korzystała z biblioteki Twilio dla języka Python na dwa sposoby:

* Zainstaluj bibliotekę Twilio dla języka Python jako pakiet PIP. Można go zainstalować za pomocą następujących poleceń:
   
        $ pip install twilio

    — Lub —

* Pobierz bibliotekę Twilio dla języka Python z usługi GitHub ([https://github.com/twilio/twilio-python][twilio_python]) i zainstaluj ją w następujący sposób:

        $ python setup.py install

Po zainstalowaniu biblioteki Twilio dla języka Python możesz ją `import` w plikach w języku Python:

        import twilio

Aby uzyskać więcej informacji, zobacz [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a id="howto_make_call"></a>Instrukcje: wykonywanie wywołania wychodzącego
Poniżej pokazano, jak wykonać połączenie wychodzące. Ten kod używa również witryny dostarczonej przez Twilio do zwrócenia odpowiedzi Twilio Markup Language (TwiML). Przed uruchomieniem kodu Zastąp wartości dla numerów telefonów **from_number** i **to_number** i upewnij się, że zweryfikowano numer telefonu **from_number** dla konta Twilio.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Jak wspomniano, ten kod używa witryny dostarczonej przez Twilio do zwrócenia odpowiedzi TwiML. Zamiast tego możesz użyć własnej lokacji, aby zapewnić odpowiedź TwiML; Aby uzyskać więcej informacji, zobacz artykuł [jak zapewnić odpowiedzi TwiML z własnej witryny sieci Web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Instrukcje: wysyłanie wiadomości SMS
Poniżej przedstawiono sposób wysyłania wiadomości SMS przy użyciu klasy `TwilioRestClient`. Numer **from_number** jest dostarczany przez Twilio dla kont próbnych do wysyłania wiadomości SMS. Przed uruchomieniem kodu należy zweryfikować numer **to_number** dla konta Twilio.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Instrukcje: udostępnianie odpowiedzi TwiML z własnej witryny sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API Twilio, Twilio wyśle żądanie do adresu URL, który powinien zwrócić odpowiedź TwiML. Powyższy przykład używa adresu URL podanego przez Twilio [https://twimlets.com/message][twimlet_message_url]. (Podczas gdy TwiML jest przeznaczony do użytku przez Twilio, można wyświetlić go w przeglądarce. Na przykład kliknij [https://twimlets.com/message][twimlet_message_url] , aby zobaczyć pusty element `<Response>`; w innym przykładzie kliknij [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] , aby zobaczyć element `<Response>` zawierający element `<Say>`.)

Zamiast polegać na adresie URL podanego przez Twilio, można utworzyć własną lokację, która zwraca odpowiedzi HTTP. Lokację można utworzyć w dowolnym języku, który zwraca odpowiedzi XML; w tym temacie założono, że przy użyciu języka Python utworzysz TwiML.

W poniższych przykładach zostanie wyświetlona odpowiedź TwiML z informacją o tym, **Hello World** w wywołaniu.

Z kolbą:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Z Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Jak widać w powyższym przykładzie, odpowiedź TwiML jest po prostu dokumentem XML. Biblioteka Twilio dla języka Python zawiera klasy, które wygenerują TwiML. Poniższy przykład tworzy równoważną odpowiedź, jak pokazano powyżej, ale używa modułu `twiml` w bibliotece Twilio dla języka Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Aby uzyskać więcej informacji na temat TwiML, zobacz [https://www.twilio.com/docs/api/twiml][twiml_reference].

Po skonfigurowaniu aplikacji w języku Python w celu zapewnienia odpowiedzi TwiML Użyj adresu URL aplikacji jako adresu URL przesłanego do metody `client.calls.create`. Na przykład jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożoną w usłudze hostowanej na platformie Azure, możesz użyć jej adresu URL jako elementu webhook, jak pokazano w następującym przykładzie:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Instrukcje: korzystanie z dodatkowych usług Twilio Services
Oprócz przykładów przedstawionych tutaj Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do korzystania z dodatkowych funkcji Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API Twilio][twilio_api].

## <a id="NextSteps"></a>Następne kroki
Teraz, gdy znasz już podstawy usługi Twilio, Skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń Twilio][twilio_security_guidelines]
* [Twilio prowadnice porady i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu Twilio][twilio_quickstarts]
* [Twilio w serwisie GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną Twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
