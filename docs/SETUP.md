# 📚 Documentación - Stripe Pagos

## 📋 Tabla de Contenidos
1. [Setup Completo](#setup-completo)
2. [API Reference](#api-reference)
3. [Webhooks](#webhooks)
4. [Ejemplos](#ejemplos)

## 🚀 Setup Completo

### Requisitos Previos
- Node.js 14+ y npm
- Python 3.8+ y pip
- Cuenta en Stripe (https://stripe.com)
- Git

### 1. Obtener Credenciales de Stripe

1. Ve a [https://dashboard.stripe.com](https://dashboard.stripe.com)
2. Ve a "Developers" > "API Keys"
3. Copia:
   - **Secret Key**: `sk_test_...`
   - **Publishable Key**: `pk_test_...`
4. Ve a "Webhooks" y crea un webhook
5. Copia el **Webhook Secret**: `whsec_...`

### 2. Backend Node.js + Express

```bash
cd backend-nodejs
cp .env.example .env
```

Edita `.env` con tus credenciales:
```env
STRIPE_SECRET_KEY=sk_test_YOUR_KEY
STRIPE_PUBLISHABLE_KEY=pk_test_YOUR_KEY
STRIPE_WEBHOOK_SECRET=whsec_YOUR_SECRET
DATABASE_URL=mongodb://localhost:27017/stripe-pagos
PORT=3000
NODE_ENV=development
JWT_SECRET=your_jwt_secret_key
```

Instala dependencias e inicia:
```bash
npm install
npm run dev
```

### 3. Backend Python + Flask

```bash
cd backend-python
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate
cp .env.example .env
```

Edita `.env` con tus credenciales:
```env
STRIPE_SECRET_KEY=sk_test_YOUR_KEY
STRIPE_WEBHOOK_SECRET=whsec_YOUR_SECRET
PORT=5000
```

Instala dependencias e inicia:
```bash
pip install -r requirements.txt
python run.py
```

### 4. Frontend React

```bash
cd frontend
cp .env.example .env
```

Edita `.env`:
```env
REACT_APP_STRIPE_PUBLISHABLE_KEY=pk_test_YOUR_KEY
REACT_APP_API_URL=http://localhost:3000/api
```

Instala dependencias e inicia:
```bash
npm install
npm start
```

## 📡 API Reference

### Pagos

#### Crear Payment Intent
```
POST /api/payments/create
Content-Type: application/json

{
  "amount": 99.99,
  "currency": "usd",
  "customer_email": "usuario@email.com",
  "description": "Descripción del pago",
  "metadata": {
    "order_id": "12345"
  }
}
```

**Respuesta exitosa:**
```json
{
  "success": true,
  "message": "Payment intent creado exitosamente",
  "clientSecret": "pi_...",
  "paymentIntentId": "pi_..."
}
```

#### Confirmar Pago
```
POST /api/payments/confirm
Content-Type: application/json

{
  "paymentIntentId": "pi_..."
}
```

#### Obtener Detalles del Pago
```
GET /api/payments/:paymentId
```

#### Reembolsar Pago
```
POST /api/payments/:paymentId/refund
Content-Type: application/json

{
  "amount": 50.00
}
```

### Suscripciones

#### Crear Suscripción
```
POST /api/subscriptions/create
Content-Type: application/json

{
  "email": "usuario@email.com",
  "priceId": "price_1A"
}
```

#### Cancelar Suscripción
```
PUT /api/subscriptions/:subscriptionId/cancel
Content-Type: application/json

{
  "cancelAtPeriodEnd": true
}
```

#### Obtener Detalles de Suscripción
```
GET /api/subscriptions/:subscriptionId
```

#### Obtener Suscripciones de Cliente
```
GET /api/subscriptions/customer/:email
```

## 🔔 Webhooks

### Eventos Soportados

- `payment_intent.succeeded` - Pago exitoso
- `payment_intent.payment_failed` - Pago fallido
- `customer.subscription.created` - Suscripción creada
- `customer.subscription.updated` - Suscripción actualizada
- `customer.subscription.deleted` - Suscripción cancelada
- `invoice.paid` - Factura pagada
- `invoice.payment_failed` - Error en pago de factura

### Configurar Webhook en Stripe

1. Ve a Dashboard de Stripe
2. "Developers" > "Webhooks"
3. Click en "Add endpoint"
4. URL: `https://tudominio.com/api/webhooks/stripe`
5. Selecciona los eventos
6. Copia el Webhook Secret y agregalo a `.env`

## 💡 Ejemplos

### Ejemplo 1: Procesar Pago Simple

```javascript
// Frontend
const paymentData = await createPaymentIntent(99.99, 'user@email.com', 'Mi Producto');

const { paymentIntent } = await stripe.confirmCardPayment(
  paymentData.clientSecret,
  {
    payment_method: { card: elements.getElement(CardElement) }
  }
);

if (paymentIntent.status === 'succeeded') {
  console.log('✅ Pago exitoso!');
}
```

### Ejemplo 2: Crear Suscripción

```javascript
const result = await createSubscription('user@email.com', 'price_pro');

if (result.success) {
  console.log('✅ Suscripción creada:', result.subscription.subscriptionId);
}
```

### Ejemplo 3: Cancelar Suscripción

```javascript
const result = await cancelSubscription('sub_...');

if (result.success) {
  console.log('✅ Suscripción cancelada');
}
```

## 🔐 Seguridad

### Mejores Prácticas

1. **Nunca expongas tu Secret Key** en el frontend o repositorio
2. **Usa variables de entorno** para todas las credenciales
3. **Verifica webhooks** con la firma de Stripe
4. **Usa JWT** para autenticación
5. **HTTPS en producción** siempre
6. **Valida datos** en frontend y backend

## 🐛 Troubleshooting

### Error: "Invalid API Key"
- Verifica que estés usando la Secret Key (no Publishable Key)
- Asegúrate que esté en la variable de entorno correcta

### Error: "Webhook signature verification failed"
- Verifica el Webhook Secret
- Asegúrate que sea el correcto del evento

### Error: "CORS"
- Configura CORS_ORIGIN en .env
- Asegúrate que el frontend esté en la URL permitida

## 📞 Soporte

Para más información:
- Documentación de Stripe: https://stripe.com/docs
- Issues del repositorio: [GitHub Issues]
- Email: talle2beto@gmail.com

---

**Última actualización:** Junio 2024
