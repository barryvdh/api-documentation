.. _v1/payments-create:

Payments API v1: Create payment
===============================
.. warning:: This is the documentation of the v1 API. The documentation for creating payments in the new v2 API can be
             found :ref:`here <v2/payments-create>`. For more information on the v2 API, refer to our
             :ref:`v2 migration guide <migrate-to-v2>`.

``POST`` ``https://api.mollie.com/v1/payments``

Authentication: :ref:`API keys <guides/authentication>`, :ref:`OAuth access tokens <oauth/overview>`

Payment creation is elemental to the Mollie API: this is where most payment implementations start off. Note optional
parameters are accepted for certain payment methods.

To wrap your head around the payment process, an explanation and flow charts can be found in the
:ref:`Overview <overview>`.

Parameters
----------
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``amount``
       | decimal
     - The amount in EURO that you want to charge, e.g. 100.00 if you would want to charge €100.00.

   * - | ``description``
       | string
     - The description of the payment you're creating. This will be shown to the consumer on their card or bank
       statement when possible, and in any exports you generate.

       We recommend you use the order number so that you can always link the payment to the order. This is particularly
       useful for bookkeeping.

   * - | ``redirectUrl``
       | string
     - `The URL the customer will be redirected to after the payment process. It could make sense for the
       ``redirectUrl`` to contain a unique identifier – like your order ID – so you can show the right page referencing
       the order when your customer returns.

   * - | ``webhookUrl``
       | string
     - Set the webhook URL, where we will send payment status updates to.

       .. note:: The ``webhookUrl`` must be reachable from Mollie's point of view. If you want to use webhook during
          development on ``localhost``, you must use a tool like
          `ngrok <https://lornajane.net/posts/2015/test-incoming-webhooks-locally-with-ngrok>`_ to have the webhooks
          delivered to your local machine.

   * - | ``locale``
       | string
     - Optional – Allow you to preset the language to be used in the payment screens shown to the consumer. Setting a
       locale is highly recommended and will greatly improve your conversion rate. When this parameter is omitted, the
       browser language will be used instead if supported by the payment method. You can provide any ISO 15897 locale,
       but our payment screen currently only support the following languages:

       Possible values: ``en_US`` ``de_AT`` ``de_CH`` ``de_DE`` ``es_ES`` ``fr_BE`` ``fr_FR`` ``nl_BE`` ``nl_NL``

   * - | ``method``
       | string
     - Optional – Normally, a payment method selection screen is shown. However, when using this parameter, your
       customer will skip the selection screen and will be sent directly to the chosen payment method. The parameter
       enables you to fully integrate the payment method selection into your website, however note Mollie's country
       based conversion optimization is lost.

       Possible values: ``banktransfer`` ``belfius`` ``bitcoin`` ``creditcard`` ``directdebit`` ``giftcard`` ``ideal``
       ``inghomepay`` ``kbc`` ``mistercash`` ``paypal`` ``paysafecard`` ``sofort``

   * - | ``metadata``
       | object
     - Optional – Provide any data you like in JSON notation, and we will save the data alongside the payment. Whenever
       you fetch the payment with our API, we'll also include the metadata. You can use up to approximately 1kB.

   * - | ``recurringType``
       | string
     - Optional – Enables recurring payments. If set to first, a first payment for the customer is created, allowing the
       customer to agree to automatic recurring charges taking place on their account in the future. If set to
       ``recurring``, the customer's card is charged automatically.

       Possible values: ``first`` ``recurring``

   * - | ``customerId``
       | string
     - Optional – The ID of the :ref:`Customer <v1/customers-create>` for whom the payment is being created. This is
       used for recurring payments and :ref:`single click payments <guides/checkout>`.

   * - | ``mandateId``
       | string
     - Optional – When creating recurring payments, the ID of a specific :ref:`Mandate <v1/mandates-create>` may be
       supplied to indicate which of the consumer's accounts should be credited.

Payment method specific parameters
----------------------------------
If you specify the ``method`` parameter, optional parameters may be available for the payment method. If no method is
specified, you can still send the optional parameters and we will apply them when the consumer selects the relevant
payment method.

Bank transfer
^^^^^^^^^^^^^
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``billingEmail``
       | string
     - Optional – Consumer's email address, to automatically send the bank transfer details to. **Please note:** the
       payment instructions will be sent immediately when creating the payment. If you don't specify the ``locale``
       parameter, the email will be sent in English, as we haven't yet been able to detect the consumer's browser
       language.

   * - | ``dueDate``
       | string
     - Optional - The date the payment should :ref:`expire <guides/payment-status-changes>`, in ``YYYY-MM-DD`` format.
       **Please note:** the minimum date is tomorrow and the maximum date is 100 days after tomorrow.

   * - | ``locale``
       | string
     - Optional – The locale will determine the target bank account the customer has to transfer the money to. We have
       dedicated bank accounts for Belgium, France, Germany and The Netherlands. Having the customer use a local bank
       account greatly increases the conversion and speed of payment.

       Possible values: ``en_US`` ``de_AT`` ``de_CH`` ``de_DE`` ``es_ES`` ``fr_BE`` ``fr_FR`` ``nl_BE`` ``nl_NL``

Bitcoin
^^^^^^^
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``billingEmail``
       | string
     - Optional – The email address of the customer. This is used when handling invalid transactions (wrong amount
       transferred, transfer of expired or cancelled payments, et cetera).

Credit card
^^^^^^^^^^^
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``billingAddress``
       | string
     - Optional – The card holder's address. We advise to provide these details to improve the credit card fraud
       protection, and thus improve conversion.

   * - | ``billingCity``
       | string
     - Optional – The card holder's city.

   * - | ``billingRegion``
       | string
     - Optional – The card holder's region.

   * - | ``billingPostal``
       | string
     - Optional – The card holder's postal code.

   * - | ``billingCountry``
       | string
     - Optional – The card holder's country in `ISO 3166-1 alpha-2 <https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2>`_
       format.

   * - | ``shippingAddress``
       | string
     - Optional – The shipping address. We advise to provide these details to improve the credit card fraud protection,
       and thus improve conversion.

   * - | ``shippingCity``
       | string
     - Optional – The city of the shipping address.

   * - | ``shippingRegion``
       | string
     - Optional – The region of the shipping address.

   * - | ``shippingPostal``
       | string
     - Optional – The postal code of the shipping address.

   * - | ``shippingCountry``
       | string
     - Optional – The country of the shipping address, in
       `ISO 3166-1 alpha-2 <https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2>`_ format.

Gift cards
^^^^^^^^^^
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``issuer``
       | string
     - Optional – The gift card brand to use for the payment. These issuers are not dynamically available through the
       Issuers API, but can be retrieved by using the ``issuers`` include in the Methods API. If you need a brand not in
       the list, contact our support department. If only one issuer is activated on your account, you can omit this
       parameter.

       Possible values: ``nationalebioscoopbon`` ``nationaleentertainmentcard`` ``kunstencultuurcadeaukaart``
       ``podiumcadeaukaart`` ``vvvgiftcard`` ``webshopgiftcard`` ``yourgift``

   * - | ``voucherNumber``
       | string
     - Optional – The card number on the gift card.

   * - | ``voucherPin``
       | string
     - Optional – The PIN code on the gift card. Only required if there is a PIN code printed on the gift card.

iDEAL
^^^^^
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``issuer``
       | string
     - Optional – An iDEAL issuer ID, for example ``ideal_INGBNL2A``. The returned payment URL will deep-link into the
       specific banking website (ING Bank, in this example). The full list of issuers can be retrieved via the
       :ref:`Issuers API <v1/issuers-list>`.

KBC/CBC Payment Button
^^^^^^^^^^^^^^^^^^^^^^
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``description``
       | string
     - When KBC/CBC is chosen as the payment method, the description will be truncated to 13 characters.

   * - | ``issuer``
       | string
     - Optional – The issuer to use for the KBC/CBC payment. These issuers are not dynamically available through the
       Issuers API, but can be retrieved by using the ``issuers`` include in the Methods API.

       Possible values: ``kbc`` ``cbc``

PayPal
^^^^^^
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``shippingAddress``
       | string
     - Optional – The shipping address. We advise to provide these details to improve PayPal's fraud protection, and
       thus improve conversion. The maximum character length is 128.

   * - | ``shippingCity``
       | string
     - Optional – The city of the shipping address. The maximum character length is 100.

   * - | ``shippingRegion``
       | string
     - Optional – The region of the shipping address. The maximum character length is 100. This field is required if the
       ``shippingCountry`` is one of the following countries: ``AR`` ``BR`` ``CA`` ``CN`` ``ID`` ``IN`` ``JP`` ``MX``
       ``TH`` ``US``

   * - | ``shippingPostal``
       | string
     - Optional – The postal code of the shipping address. The maximum character length is 20.

   * - | ``shippingCountry``
       | string
     - Optional – The country of the shipping address, in
       `ISO 3166-1 alpha-2 <https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2>`_ format.

paysafecard
^^^^^^^^^^^
.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``customerReference``
       | string
     - Optional – Used for consumer identification. For example, you could use the consumer's IP address.

SEPA Direct Debit
^^^^^^^^^^^^^^^^^
**One-off SEPA Direct Debit payments using Mollie Checkout can only be created if this is enabled on your account.** In
general, it is not very useful for web shops but may be useful for charities.

If you want to use recurring payments, take a look at our :ref:`Recurring payments guide <guides/recurring>`.

.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``consumerName``
       | string
     - Optional - Beneficiary name of the account holder. Only available if one-off payments are enabled on your
       account. Will pre-fill the beneficiary name in the checkout screen if present.

   * - | ``consumerAccount``
       | string
     - Optional - IBAN of the account holder. Only available if one-off payments are enabled on your account. Will
       pre-fill the IBAN in the checkout screen if present.

Mollie Connect/OAuth parameters
-------------------------------
If you're creating an app with Mollie Connect/OAuth, the only mandatory extra parameter is the ``profileId`` parameter.
With it, you can specify which profile the payment belongs to. Organizations can have multiple profiles for each of
their websites. See :ref:`Profiles API <v1/profiles-get>` for more information.

.. list-table::
   :header-rows: 0
   :widths: auto

   * - | ``profileId``
       | string
     - The payment profile's unique identifier, for example ``pfl_3RkSN1zuPE``. This field is mandatory.

   * - | ``testmode``
       | boolean
     - Optional – Set this to ``true`` to make this payment a test payment.

   * - | ``applicationFee``
       | object
     - Optional – Adding an Application Fee allows you to charge the merchant a small sum for the payment and transfer
       this to your own account. Set the ``applicationFee`` parameter as a small object with it’s own amount and
       description. The application fee amount must be at least about €1.00 less than the payment's ``amount``
       parameter.

       .. list-table::
          :header-rows: 0
          :widths: auto

          * - | ``amount``
              | decimal
            - The amount in EUR that the app wants to charge, e.g. ``10.00`` if the app would want to charge €10.00.

              Note that you will need to invoice the merchant yourself. We will only collect the amount from the
              merchant and settle the amount with you.

          * - | ``description``
              | string
            - The description of the application fee. This will appear on settlement reports to the merchant and to you.

QR codes
--------
To create a payment with a QR code embedded in the API response, call the API endpoint with an
include request for ``details.qrCode`` in the query string:

``POST https://api.mollie.com/v1/payments?include=details.qrCode``

QR codes can be generated for iDEAL, Bitcoin, Bancontact and bank transfer payments.

Refer to the :ref:`Get payment <v1/payments-get>` reference to see what the API response looks like when the QR code is
included.

Response
--------
``201`` ``application/json; charset=utf-8``

A payment object is returned, as described in :ref:`Get payment <v1/payments-get>`.

Example
-------

Request
^^^^^^^
.. code-block:: bash

   curl -X POST https://api.mollie.com/v1/payments \
       -H "Authorization: Bearer test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM" \
       -d "amount=10.00" \
       -d "description=My first payment" \
       -d "redirectUrl=https://webshop.example.org/order/12345/" \
       -d "webhookUrl=https://webshop.example.org/payments/webhook/" \
       -d "metadata[order_id]=12345"

Response
^^^^^^^^
.. code-block:: http

   HTTP/1.1 201 Created
   Content-Type: application/json; charset=utf-8

   {
       "resource": "payment",
       "id": "tr_7UhSN1zuXS",
       "mode": "test",
       "createdDatetime": "2018-03-16T14:36:44.0Z",
       "status": "open",
       "expiryPeriod": "PT15M",
       "amount": "10.00",
       "description": "My first payment",
       "metadata": {
           "order_id": "12345"
       },
       "locale": "nl",
       "profileId": "pfl_QkEhN94Ba",
       "links": {
           "paymentUrl": "https://www.mollie.com/payscreen/select-method/7UhSN1zuXS",
           "redirectUrl": "https://webshop.example.org/order/12345/",
           "webhookUrl": "https://webshop.example.org/payments/webhook/"
       }
   }