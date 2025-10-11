# SAPUI5 Workbook — Boas Práticas de Projeto

## Sumário

1. Convenções de Nomenclatura (Notação Húngara)
2. Modularização do Código
3. Uso de JSDoc
4. Estrutura XML e Extensão Red Hat
5. Classes Auxiliares
6. Fragmentos de View
7. Internacionalização (i18n)
8. Evitar Uso de APIs Depreciadas
9. Versionamento com Git
10. Tratamento de Erros e Exibição de Mensagens

---

## 📘 1. Convenções de Nomenclatura

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
-   Utilize a **notação húngara** para que seu código mantenha coerência com as api standard do sapui5 (cujo código e documentação são escritos em notação húngara):

    ![alt text](image-4.png)

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

## 2. Modularização do Código

Evite funções longas e controladores inchados. Divida o código em **módulos reutilizáveis** e **funções com responsabilidades claras**.

**Exemplo:**

```js
handleSave: async function() {
    try {
        const oData = this._collectFormData();
        await this._validateData(oData);
        await this._saveData(oData);
        MessageToast.show(this.getText("msgSaved"));
    } catch (oError) {
        MessageBox.error(oError.message);
    }
},

_collectFormData: function() {
    // ... coleta dados do modelo
},

_validateData: function(oData) {
    // ... validações de negócio
},

_saveData: async function(oData) {
    // ... chamada ao backend
}
```

---

## 3. Uso de JSDoc

Documente **todas as funções públicas e privadas**. O JSDoc deve conter:

-   **@param**: tipo e descrição dos parâmetros;
-   **@returns**: tipo e descrição do retorno;
-   **@private / @public**: visibilidade;
-   **Descrição em lista de pontos**, explicando a lógica.

**❌ Mau Exemplo:**

```js

_validateData: async function(oData) {

    //Verifica campo obrigatório de nome do comprador
    if (!oData.name)
        throw new Error(this.getText("errMissingName"));

    //Verifica campo obrigatório de número de cadastro do comprador
    if (!oData.personNumber)
        throw new Error(this.getText("errMissingPersonNumber"));

    //Verifica campo obrigatório de Ordem de compra
    if (!oData.purchaseOrder)
        throw new Error(this.getText("errMissingCenter"));

    //Verifica campo obrigatório de Centro
    if (!oData.center)
        throw new Error(this.getText("errMissingPurchaseOrder"));

    //Verifica em paralelo a consistência entre centro e número do comprador
    //e também os materiais da ordem de compra
    const aResults = await Promise.allSettled([
        this._validateCenter(oData?.center, oData?.personNumber),
        this._validateMaterials(oData?.purchaseOrder, oData?.materials)
    ]);

    //Procura o primeiro resultado que tenha sido rejeitado. Se achar, lança erro.
    const oFailed = aResults.find(item=>item.status === 'rejected');
    if(oFailed)
        throw new Error(oFailed?.reason?.details);

},

```

**🟩 Bom Exemplo:**

```js
/**
 * Valida os dados do formulário antes do envio.
 * - Verifica campos obrigatórios:
 *      - nome do comprador
 *      - Centro
 *      - Ordem de Compra
 * - Verifica em paralelo:
 *      - a consistência do Centro com o número de cadastro do comprador
 *      - a consistência entre a Ordem de Compra e os materiais indicados
 * @async
 * @param {object} oData - Dados coletados do formulário.
 * @returns {Promise<void>} - Promise implícita que quando resolvida indica que os dados
 * foram validados com sucesso
 * @throws {Error} - Lança erro em caso de inconsistência.
 * @private
 */
_validateData: async function(oData) {

    if (!oData.name)
        throw new Error(this.getText("errMissingName"));

    if (!oData.personNumber)
        throw new Error(this.getText("errMissingPersonNumber"));

    if (!oData.purchaseOrder)
        throw new Error(this.getText("errMissingCenter"));

    if (!oData.center)
        throw new Error(this.getText("errMissingPurchaseOrder"));

    const aResults = await Promise.allSettled([
        this._validateCenter(oData?.center, oData?.personNumber),
        this._validateMaterials(oData?.purchaseOrder)
    ]);

    const oFailed = aResults.find(item=>item.status === 'rejected');
    if(oFailed)
        throw new Error(oFailed?.reason?.details);

},
```

---

## 4. Classes Auxiliares

Crie classes auxiliares para lógica reutilizável, como formatações, validações e integrações.

**Exemplo:**

Classe auxiliar **`CriarRegistro.js`**

```javascript
/**
 * Métodos para controle do popup de Criação de registro
 *
 * @module CriarRegistro
 * @public
 */
sap.ui.define([], function () {
	"use strict";

	return {
		/**
		 * Realiza atividades de inicialização e abre o popup de criação de registro
		 *
		 * * Guarda o contexto da função invocadora na variavel global that
		 * * Carrega o fragmento do popup
		 * * Realiza inicializações importantes para o popup (controles, modelos)
		 *
		 * Atenção: define-se o this(escopo deste arquivo) como controlador do
		 * dialogo a fim de permitir uma maior simplicidade nas chamadas de
		 * funções aqui definidas.
		 * Para acessar funções da classe invocadora, basta utilizar a variável
		 * global that.
		 * @async
		 * @param {object} oThis Contexto da funçao invocadora
		 * @returns {Promise<void>} Promise que é resolvida após a correta abertura do popup
		 * @public
		 */
		openDialog: async function (oThis) {
			try {
				that = oThis;

				oDialog = await that.getExtensionAPI().loadFragment({
					name: "com.projeto.nomedoapp.ext.view.fragments.dialogs.CriarRegistroDialog",
					controller: this,
				});

				oDialog.open();
				this._init(sInput, bNota);
			} catch (error) {
				console.error(error);
				that.displayMessage(error);
			}
		},

		//...outros métodos...
	};
});
```

Classe que usa uma funcionalidade da auxiliar: **`Main.controller.js`**

```js
sap.ui.define(
	["com/projeto/nomedoapp/ext/main/classes/BaseController", "com/projeto/nomedoapp/ext/main/classes/CriarRegistro"],
	function (BaseController, CriarRegistro) {
		"use strict";

		var oEditModel, oFilterBar;

		return BaseController.extend("com.vale.gestaodadosmestres.ext.main.Main", {
			onInit: function () {
				/*...*/
			},
			// ... outros métodos ...

			onCriarNovoRegistro: function () {
				try {
					CriarRegistro.openDialog(this);
				} catch (error) {
					console.error(error);
					this.displayMessage(error);
				}
			},
		});
	}
);
```

---

## 5. Padronização do código XML de views

Evite adicionar várias propriedades de controles xml numa mesma linha. O ideal é que cada propriedade fique numa linha só, o que facilita a leitura do código e sua manutenção

**❌ Exemplo de má prática:**

```xml
<Button text="{i18n>btnAprovarCompra}" type="Emphasized" class="sapUiSmallMarginBeginEnd" visible="{editModelAprovacao>/visible}" enabled="{editModelAprovacao>/enabled}" press="onAprovarCompra" >
</Button>
```

**✅ Exemplo de boa prática:**

```xml
<Button
    text="{i18n>btnAprovarCompra}"
    type="Emphasized"
    class="sapUiSmallMarginBeginEnd"
    visible="{editModelAprovacao>/visible}"
    enabled="{editModelAprovacao>/enabled}"
    press="onAprovarCompra" />
```

Para facilitar a adoção dessa boa prática use a extensão **Red Hat XML**, habilite-a como formatador padrão para xml e configure-a para formatar automaticamente **cada atributo em uma linha separada**, bem como manter o **espaço de indentação em 1**. Dessa forma sempre que apertar Alt + shift + f o código será formatado de acordo.

![alt text](image.png)
![alt text](image-1.png)

### No arquivo de configurações ficará assim:

```json
 "xml.format.splitAttributesIndentSize": 1,
 "xml.format.splitAttributes": "splitNewLine",
 "[xml]": {
    "editor.defaultFormatter": "redhat.vscode-xml"
 },
```

---

## 6. Fragmentos de View

Para **views extensas (>200 linhas)**, modularize o layout usando **fragments** (`.fragment.xml`).

**Exemplo:**

```xml
<core:FragmentDefinition xmlns="sap.m" xmlns:core="sap.ui.core">
    <VBox>
        <Input value="{viewModel>/name}" placeholder="{i18n>phName}" />
        <Button text="{i18n>btnSubmit}" press="onSubmit" />
        <!-- outros controles -->
    </VBox>
</core:FragmentDefinition>
```

Na view:

```xml
<!-- hieraquia de controles que estruturam a página, por exemplo... -->
<content>
 <core:Fragment
    fragmentName="com.projeto.nomedoapp.ext.view.fragments.InfoBaseForm"
    type="XML" />
<content>
<!-- hieraquia de controles que estruturam a página, por exemplo... -->
```

---

## 7. Internacionalização (i18n)

Utilize o arquivo `i18n.properties` como fonte de textos, evitando hardcoding.

**Exemplo:**

```
btnSave=Salvar
msgSaved=Registro salvo com sucesso
errMissingName=O campo Nome é obrigatório
```

No controller:

```js
MessageToast.show(this.getText("msgSaved"));
```

---

## 8. Evitar Uso de APIs Depreciadas

Evite o uso de **controles, propriedades e métodos depreciados**. O SAPUI5 evolui constantemente, e funcionalidades antigas podem deixar de ser suportadas em versões futuras.

### Boas práticas:

-   Sempre verifique a documentação oficial no [SAPUI5 SDK](https://sapui5.hana.ondemand.com/sdk/#/api).
-   Cada API, controle ou propriedade indica claramente se está **@deprecated** e, em muitos casos, aponta a alternativa recomendada.

**Dica:** Tanto o Business Application Studio quanto o VS Code (por meio das extensões do Fiori Tools) indicam problemas dessa natureza:

![alt text](image-2.png)
![alt text](image-3.png)

---

## 9. Versionamento com Git

Salve as alterações com **commits claros e significativos**.

**Boas práticas:**

-   Faça commits pequenos e temáticos.
-   Alinhe com seu time o padrão de mensagens para evitar um histórico difuso.
    -   `adiciona validação de CPF no cadastro`
    -   `corrige erro no binding da tabela`
    -   `move lógica de cálculo para classe utilitária`

**Exemplo:**

```bash
git add .
git commit -m "cria fragmento de diálogo para confirmação de exclusão"
git push origin main
```

---

## 10. Tratamento de Erros e Exibição de Mensagens

O uso de `try...catch` associado à exibição de mensagens é uma ferramenta que aprimora a experiência do usuário, permitindo que, além dos erros esperados, ele também seja informado quanto a erros inesperados. Utilize-o com foco nessa estratégia **restrito a handlers que interagem diretamente com o usuário** — como ações de botões, eventos de telas e fluxos de interface.

### 💡 Conceito: Borbulhamento de Erro (Error Bubbling)

Erros lançados em funções internas **devem subir** até o nível mais alto do fluxo, onde o usuário pode ser notificado. Assim:

-   Funções internas **lançam erros**, mas **não exibem mensagens**.
-   Handlers de interface **interceptam os erros** e exibem mensagens apropriadas.

### ✅ Exemplo correto

```js
onSavePress: async function() {
    try {
        const oData = this._collectFormData();
        await this._validateData(oData);
        await this._saveData(oData);
        MessageToast.show(this.getText("msgSaved"));
    } catch (oError) {
        MessageBox.error(oError.message || this.getText("errGeneric"));
        sap.base.Log.error("Erro ao salvar dados", oError);
    }
},

_validateData: function(oData) {
    if (!oData.name) {
        throw new Error(this.getText("errMissingName"));
    }
},

_saveData: async function(oData) {
    const bOk = await this.getModel().create("/Entities", oData);
    if (!bOk) throw new Error(this.getText("errSaveFailed"));
}
```

### 📘 Diretrizes gerais

-   Use `throw new Error(...)` dentro das funções internas.
-   Use `try...catch` apenas em handlers ou camadas de UI.
-   Centralize mensagens de erro e textos no `i18n.properties`.
-   Registre detalhes técnicos de exceções com `sap.base.Log.error`.
-   Evite múltiplos `catch` aninhados.

**Benefícios:**

-   Código mais limpo e previsível.
-   Erros tratados de forma uniforme.
-   Melhor manutenção e rastreamento de falhas.

> 💬 _“Erros devem borbulhar até o ponto onde faz sentido comunicá-los — e esse ponto é sempre a interface com o usuário.”_

---
#### Versão: 1.0.0
#### Autor: Pedro Lázaro Agostinho dos Santos
