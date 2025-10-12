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