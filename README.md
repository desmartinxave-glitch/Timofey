const tg = window.Telegram.WebApp;
tg.expand();

const products = [
  { id: 1, name: "Хлеб", price: 30 },
  { id: 2, name: "Молоко", price: 45 },
  { id: 3, name: "Яблоки", price: 60 }
];

let cart = [];

const productsDiv = document.getElementById("products");
const cartUl = document.getElementById("cart");
const totalDiv = document.getElementById("total");
const checkoutBtn = document.getElementById("checkout");

/* Рендер товаров */
products.forEach(p => {
  const div = document.createElement("div");
  div.className = "product";
  div.innerHTML = `
    <span>${p.name} — ${p.price} ₴</span>
    <button onclick="addToCart(${p.id})">+</button>
  `;
  productsDiv.appendChild(div);
});

/* Добавить в корзину */
function addToCart(id) {
  const product = products.find(p => p.id === id);
  cart.push(product);
  renderCart();
}

/* Удалить из корзины */
function removeFromCart(index) {
  cart.splice(index, 1);
  renderCart();
}

/* Рендер корзины */
function renderCart() {
  cartUl.innerHTML = "";
  let total = 0;

  cart.forEach((item, index) => {
    total += item.price;

    const li = document.createElement("li");
    li.className = "cart-item";
    li.innerHTML = `
      <span>${item.name} — ${item.price} ₴</span>
      <button onclick="removeFromCart(${index})">❌</button>
    `;
    cartUl.appendChild(li);
  });

  totalDiv.innerText = `Сумма: ${total} ₴`;

  // Обновляем MainButton Telegram
  if (cart.length > 0) {
    tg.MainButton.setText(`Оплатить ${total} ₴`);
    tg.MainButton.show();
  } else {
    tg.MainButton.hide();
  }
}

/* Отправка данных в бот */
tg.MainButton.onClick(() => {
  tg.sendData(JSON.stringify({
    cart,
    total: cart.reduce((s, i) => s + i.price, 0)
  }));
});

/* Оформить заказ кнопкой */
checkoutBtn.onclick = () => tg.MainButton.click();
