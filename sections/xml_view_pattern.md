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