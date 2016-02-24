
### redisCacheName

作成する Azure Redis Cache の名前です。

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

新しい Azure Redis Cache の価格レベルです。

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Standard"
    }

テンプレートでは、このパラメーターに指定できる値 (Basic または Standard) を定義します。値が指定されない場合は既定値 (Standard) が割り当てられます。 Basic では、複数のサイズを最大 53 GB まで使用できる 1 つのノードが提供されます。
Standard では、複数のサイズを最大 53 GB まで使用できる 2 つのノード (プライマリとレプリカ) が提供され、99.9% の SLA が実現されます。

### redisCacheFamily

SKU のファミリです。

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

新しい Azure Redis Cache インスタンスのサイズです。 

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 1
    }

テンプレートには、このパラメーターに指定できる値 (0、1、2、3、4、5、または 6) を定義します。値が指定されない場合は既定値 (1) が割り当てられます。 これらの数値は、キャッシュ サイズに対応しています。 
0 = 250 MB の場合、1 = 1 GB、2 = 2.5 GB、3 = 6 GB、4 GB、5 = 5 = 26 GB、6 = 53 GB

### redisCacheVersion

新しいキャッシュの Redis サーバーのバージョンです。

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

