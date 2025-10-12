### 🔤 Notação Húngara

Sempre que possível, utilize **notação húngara** para identificar o tipo de dado da variável ou objeto.

| Prefixo | Tipo de dado                 | Exemplo        |
| ------- | ---------------------------- | -------------- |
| `s`     | String                       | `sUserName`    |
| `i`     | Integer                      | `iTotalCount`  |
| `b`     | Boolean                      | `bIsActive`    |
| `o`     | Object                       | `oPayload`     |
| `a`     | Array                        | `aItems`       |
| `d`     | Date                         | `dStartDate`   |
| `fn`    | Função                       | `fnFormatDate` |
| `p`     | Promise                      | `pIsValid`     |
| `m`     | Mapa / Dicionário            | `mFilters`     |
| `v`     | Valor genérico (pode variar) | `vResult`      |

🟩 **Boas práticas:**

-   Sempre use nomes **descritivos** e **autoexplicativos**.
-   Evite abreviações desnecessárias ou que só você entende.
-   Variáveis de controle local (ex: `i`, `j`) são aceitáveis quando usadas em loops.
-   Utilize a **notação húngara** para que seu código mantenha coerência com as api standard do sapui5 (cujo código e documentação são escritos em notação húngara):<br><br>

    ![alt text](images/image001.png)

🟩**Exemplos certos:**

```js
let oButton = new sap.m.Button();   // 'o' para object
let sName = "Pedro";                // 's' para string
let iCount = 10;                    // 'i' para integer
let aItems = [];                    // 'a' para array
let dDate = new Date();             // 'd' para objetos de data
let pPromise = new Promise(...)     //'p' para promise
```

❌**Exemplos errados:**

```js
let button = new sap.m.Button(); //ausencia de notação hungara
let sN = "Pedro"; //nome pouco expressivo, muito abreviado
let sCount = 10; //s para um número
let oItems = []; //o para um array
let dDate = "2024-01-12"; //d deve ser usado para objetos de Data, e não para uma string de data
```