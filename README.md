- 👋 Hi, I’m @Lucaaufderhaar
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
Lucaaufderhaar/Lucaaufderhaar is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
### 1. Installeer de benodigde pakketten
Voer de volgende opdrachten uit in de terminal:

```bash
npx create-react-app affiliate-webshop
cd affiliate-webshop
npm install antd redux react-redux @reduxjs/toolkit axios
```

### 2. Maak de Redux-slice aan
Maak een nieuwe map `features` aan in de `src`-map en maak een bestand `productsSlice.js` aan:

```javascript
// src/features/productsSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import axios from 'axios';

export const fetchProducts = createAsyncThunk('products/fetchProducts', async () => {
const response = await axios.get('https://fakestoreapi.com/products');
return response.data;
});

const productsSlice = createSlice({
name: 'products',
initialState: {
items: [],
status: 'idle',
error: null,
},
reducers: {},
extraReducers: (builder) => {
builder
.addCase(fetchProducts.pending, (state) => {
state.status = 'loading';
})
.addCase(fetchProducts.fulfilled, (state, action) => {
state.status = 'succeeded';
state.items = action.payload;
})
.addCase(fetchProducts.rejected, (state, action) => {
state.status = 'failed';
state.error = action.error.message;
});
},
});

export default productsSlice.reducer;
```

### 3. Configureer de Redux-store
Maak een bestand `store.js` aan in de `src`-map:

```javascript
// src/store.js
import { configureStore } from '@reduxjs/toolkit';
import productsReducer from './features/productsSlice';

const store = configureStore({
reducer: {
products: productsReducer,
},
});

export default store;
```

### 4. Maak de hoofdcomponent
Pas de `src/index.js` aan om de store te integreren:

```javascript
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import App from './App';
import 'antd/dist/antd.css'; // Ant Design CSS

ReactDOM.render(
<Provider store={store}>
<App />
</Provider>,
document.getElementById('root')
);
```

### 5. Maak de App-component
Pas de `src/App.js` aan:

```javascript
// src/App.js
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { fetchProducts } from './features/productsSlice';
import { List, Card, Typography, Spin, Alert } from 'antd';

const { Title } = Typography;

const App = () => {
const dispatch = useDispatch();
const products = useSelector((state) => state.products.items);
const productStatus = useSelector((state) => state.products.status);
const error = useSelector((state) => state.products.error);

useEffect(() => {
if (productStatus === 'idle') {
dispatch(fetchProducts());
}
}, [productStatus, dispatch]);

return (
<div style={{ padding: '20px' }}>
<Title level={2}>Trending Products</Title>
{productStatus === 'loading' && <Spin size="large" />}
{productStatus === 'failed' && <Alert message={error} type="error" />}
<List
grid={{ gutter: 16, column: 4 }}
dataSource={products}
renderItem={item => (
<List.Item>
<Card
title={item.title}
cover={<img alt={item.title} src={item.image} />}
>
<p>Price: ${item.price}</p>
<button style={{ backgroundColor: '#4CAF50', color: 'white' }}>
Buy Now
</button>
</Card>
</List.Item>
)}
/>
</div>
);
};

export default App;
```

### 6. Optionele styling
Je kunt de styling verder aanpassen in een CSS-bestand, bijvoorbeeld `src/App.css`, en deze importeren in `App.js`:

```css
/* src/App.css */
.card {
transition: transform 0.2s;
}

.card:hover {
transform: scale(1.05);
}
```

### 7. Voeg een eenvoudige navigatie toe (optioneel)
Je kunt ook een basis navigatie toevoegen. Installeer `react-router-dom`:

```bash
npm install react-router-dom
```

Pas `src/index.js` aan om de router te integreren:

```javascript
import { BrowserRouter as Router } from 'react-router-dom';

// ...

ReactDOM.render(
<Provider store={store}>
<Router>
<App />
</Router>
</Provider>,
document.getElementById('root')
);
```

### 8. Start de applicatie
Voer de volgende opdracht uit om de applicatie te starten:

```bash
npm start
```
