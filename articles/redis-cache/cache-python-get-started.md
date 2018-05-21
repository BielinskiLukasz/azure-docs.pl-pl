---
title: Przewodnik Szybki start opisuje tworzenie aplikacji Python, która korzysta z usługi Azure Redis Cache | Microsoft Docs
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć aplikację Python korzystającą z usługi Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/11/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: b66df55043e4fd29f352c6e9ec3b8674800bc4be
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-use-azure-redis-cache-with-python"></a>Szybki start: korzystanie z usługi Azure Redis Cache w połączeniu z językiem Python
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

## <a name="introduction"></a>Wprowadzenie

W tym przewodniku Szybki start przedstawiono sposób nawiązywania połączenia z usługą Azure Redis Cache przy użyciu języka Python w celu odczytywania i zapisywania danych w pamięci podręcznej. 

![Test dotyczący języka Python ukończony](./media/cache-python-get-started/cache-python-completed.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Python 2 lub Python 3](https://www.python.org/downloads/) z zainstalowanym pakietem [pip](https://pypi.org/project/pip/). 

## <a name="create-a-redis-cache-on-azure"></a>Tworzenie pamięci podręcznej Redis na platformie Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalowanie klienta redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) to interfejs języka Python dla usługi Redis Cache. Użyj dostępnego w języku Python narzędzia do obsługi pakietów *pip*, aby zainstalować pakiet redis-py. 

W poniższym przykładzie do zainstalowania pakietu redis-py w systemie Windows 10 użyto narzędzia *pip3* dla języka Python3, korzystając z wiersza polecenia programu Visual Studio 2017 Developer z podwyższonym poziomem uprawnień administratora.

    pip3 install redis

![Instalowanie klienta redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Odczytywanie i zapisywanie w pamięci podręcznej

Uruchom środowisko Python i przetestuj korzystanie z pamięci podręcznej z poziomu wiersza polecenia. Zastąp `<Your Host Name>` i `<Your Access Key>` wartościami odpowiednimi dla używanej pamięci podręcznej Redis Cache. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

## <a name="create-a-python-script"></a>Tworzenie skryptu w języku Python

Utwórz nowy plik tekstowy skryptu o nazwie *PythonApplication1.py*.

Dodaj następujący skrypt do pliku *PythonApplication1.py* i zapisz go. Ten skrypt przetestuje dostęp do pamięci podręcznej. Zastąp `<Your Host Name>` i `<Your Access Key>` wartościami odpowiednimi dla używanej pamięci podręcznej Redis Cache. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,password=myPassword,ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ") 
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Uruchom skrypt języka Python.

![Test dotyczący języka Python ukończony](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do innego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
>

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *TestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

![Usuwanie](./media/cache-web-app-howto/cache-delete-resource-group.png)

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**.

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Redis Cache.](./cache-web-app-howto.md)



<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
