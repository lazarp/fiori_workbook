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