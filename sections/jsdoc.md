Documente **todas as funções públicas e privadas**. O JSDoc deve conter no mínimo as seguintes tags:

-   **@async**: indica se a função/método é assíncrona
-   **@param**: tipo e descrição dos parâmetros;
-   **@returns**: tipo e descrição do retorno;
-   **@private / @public**: visibilidade;
-   **Descrição em lista de pontos**, explicando a lógica.

Para mais informações, confira a [documentação do jsdoc](https://jsdoc.app/).


**❌ Mau Exemplo (código com vários comentários em meio às instruções):**

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

**🟩 Bom Exemplo (comentários isolados):**

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
<br>
Observação: Comentários em meio ao código podem ser usados para explicar pontualmente lógica ou mecanismos complexos.