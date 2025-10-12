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
        sap.base.Log.error("Erro ao salvar dados", oError);
        MessageBox.error(oError.message || this.getText("errGeneric"));
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
-   Use `try...catch` com exibição de mensagem de erro apenas em event handlers.
-   Centralize mensagens de erro e textos no `i18n.properties`.
-   Registre detalhes técnicos de exceções com `sap.base.Log.error`. Eles servem para garantir que o erro vai estar registrado no console de desenvolvedor do browser.
-   Evite múltiplos `catch` aninhados.

**Benefícios:**

-   Código mais limpo e padronizado.
-   Erros tratados de forma uniforme.
-   Melhor manutenção e rastreamento de falhas.
