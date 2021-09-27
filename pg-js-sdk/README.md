# GlobalPay Redeban JS SDK
===================


## Descripción
Payment Gateway JS SDK es una biblioteca que permite a los desarrolladores conectarse fácilmente a la API de GlobalPay Redeban para tokenizar tarjetas con el nivel más seguro de PCI.


## Instalación 
Deberá incluir la dependencia del SDK `payment_sdk_stable.min.js` en su página web especificando como charset "UTF-8".

```html
<!-- PG JS SDK -->
<script src="https://cdn.globalpay.com.co/ccapi/sdk/payment_sdk_stable.min.js" charset="UTF-8"></script>
```


## Uso

Para ver ejemplos prácticos del uso del SDK de Payment Gateway JS, consulte lo siguiente: [ejemplos](https://github.com/globalpayredeban/pg-docs/tree/main/pg-js-sdk/examples).

### Referencia de Tokenización
Nuestra biblioteca le permite implementar de la manera fácil un formulario para tokenizar tarjetas con nosotros. Dicho formulario es dinámico y responde para cada tarjeta, es decir, implemente este formulario para cualquier tipo de tarjeta que admitamos.

Para realizar la implementación siga los siguientes pasos:
1. Cree un elemento para contener el formulario dinamico.
2. Cree una instancia de [PaymentGateway](#Clase-PaymentGateway) con los parámetros requeridos.
3. Genere la referencia de tokenización con los datos requeridos. [generate_tokenize](#Función-generate-tokenize)
4. Defina el evento para ejecutar la acción para tokenizar [tokenize](#Función-tokenize).
Solo necesita crear un contenedor y declararlo cuando requiera que se inserte un formulario dinámico con todas las validaciones requeridas para capturar los datos sensibles.
```html
<!-- 1. Create an element to contain the dynamic form. -->
<body>
  <div id='tokenize_example'></div>
  <div id="messages"></div>
  <div class="button_container">
    <button id='tokenize_btn' class='btn'>Save card</button>
  </div>
</body>
<script>
  (function() {
    let submitButton = document.querySelector('#tokenize_btn');
    let submitInitialText = submitButton.textContent;
    // 2. Instance the [PaymentGateway](#PaymentGateway-class) with the required parameters.
    let pg_sdk = new PaymentGateway(environment, application_code, application_key);
    let tokenize_data = {
      locale: 'en',
      user: {
        id: '117',
        email: 'jhon@doe.com',
      },
      configuration: {
        default_country: 'COL',
      },
    }

    let incompleteFormCallback = message => {
      // document.getElementById('messages').innerHTML = JSON.stringify(message)
      submitButton.innerText = submitInitialText;
      submitButton.removeAttribute("disabled");
    }

    let responseCallback = response => {
      document.getElementById('messages').innerHTML = JSON.stringify(response)
      submitButton.innerText = 'Save new card';
      submitButton.removeAttribute("disabled");
      submitButton.addEventListener('click', event => {
        window.location.reload();
      });
    }
 
    // 3. Generate the tokenization reference with the required data. [generate_tokenize](#generate_tokenize-function)
    pg_sdk.generate_tokenize(tokenize_data, '#tokenize_example', responseCallback, incompleteFormCallback);

    // 4. Define the event to execute the [tokenize](#tokenize-function) action.
    submitButton.addEventListener('click', event => {
      submitButton.innerText = 'Card Processing...';
      submitButton.setAttribute("disabled", "disabled");
      pg_sdk.tokenize();
      event.preventDefault();
    });
  })();
</script>
```
 
### Clase PaymentGateway
Contiene todas las funciones para conectarse con la pasarela de pago y debe tener una instancia con los siguientes parámetros:

| Field              | Description                        | Type   | Required |
| :----------------- | :--------------------------------- |:------ | :------- |
| Environment        | Ambiente a utilizar (stg, prod) | String |     X    |
| Application Code   | Proporcionado por la pasarela de pago    | String |     X    |
| Application Key    | Proporcionado por la pasarela de pago    | String |     X    |


### Función Generate Tokenize
Ejecute esta función para generar el formulario dinámico e insértelo dentro del contenedor especificado.

Use los siguientes parametros:

| Campo                       | Descripción                                                                                          | Tipo     | Requerido |
| :-------------------------- | :--------------------------------------------------------------------------------------------------- |:-------- | :------- |
| [Tokenize data](#Tokenizar-datos)|                                                                                                   | Object   |     X    |
| Container query selector    | Query selector identificando el elemento html                                                         | String   |     X    |
| Response callback           | Callback para recibir el token                                                                        | function |     X    |
| Incomplete form callback    | Callback para ejecutar cuando se solicita [tokenize](#Función-tokenize) pero el formulario está incompleto | function |     X    |


### Función Tokenize
Ejecute esta función para solicitar la tokenización, esta función valida por defecto todos los datos establecidos por el cliente en el formulario. En caso de que los datos estén incompletos o sean incorrectos, se invocará la devolución de llamada del formulario incompleto.

### Tokenizar datos

```javascript
    let tokenize_data = {
      locale: 'en',
      user: {
        id: '117',
        email: 'jhon@doe.com',
      },
      configuration: {
        default_country: 'COL',
      },
    }
```

| Campo                                   | Descripción                                                                 | Tipo   | Requerido |
| :-------------------------------------- | :-------------------------------------------------------------------------- |:------ | :------- |
| locale                                  | Idioma a utilizar (pt, es, en)                                                | String |          |
| user.id                                 | Identificador de cliente. Este es el identificador que usa dentro de su aplicación | String |      X   |
| user.email                              | Correo electrónico del comprador, con formato de correo electrónico válido                                       | String |      X   |
| configuration.default_country           | País en formato ISO 3166-1 Alpha 3                                        | String |          |
| configuration.icon_colour               | Color de los iconos                                                                 | String |          |
| configuration.use_dropdowns             | Use menús desplegables para establecer la fecha de vencimiento de la tarjeta                               | String |          |
| configuration.exclusive_types           | Definir tipos de tarjetas permitidos                                                  | String |          |
| configuration.invalid_card_type_message | Definir un mensaje personalizado para mostrar para tipos de tarjetas no válidas                      | String |          |
