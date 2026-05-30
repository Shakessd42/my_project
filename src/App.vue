<script>
import Header from './components/header.vue'
import Footer from './components/footer.vue'

export default {
    name: 'App',

    components: {
        Header,
        Footer
    },

    data() {
        const savedCart = localStorage.getItem('motors-cart')
        const savedBudget = localStorage.getItem('moto-budget')

        let startCart = []
        if (savedCart) {
            startCart = JSON.parse(savedCart)
        }

        let startBudget = 500000
        if (savedBudget) {
            startBudget = parseInt(savedBudget)
        }

        return {
            motorcycles: [
                { id: 1, name: 'Мотоцикл GR8 f300a (4t)',       price: 330182, oldPrice: 399990, image: '/photos/gr8 f300a.png',         monthly: 30000, credit: 12000, cashback: 36000 },
                { id: 2, name: 'Мотоцикл GR8 t300l',             price: 431990, oldPrice: 479990, image: '/photos/gr8 t300l.png',         monthly: 36000, credit: 14400, cashback: 43200 },
                { id: 3, name: 'Мотоцикл PROGASI HARDCORE',      price: 419990, oldPrice: null,   image: '/photos/progasi hardcore.jpg',  monthly: 35000, credit: 14000, cashback: 42000 },
                { id: 4, name: 'Мотоцикл Progasi RACE 300 AIR',  price: 289990, oldPrice: null,   image: '/photos/progasi 300 air.jpeg', monthly: 24170, credit: 9670,  cashback: 29000 },
                { id: 5, name: 'Мотоцикл KAYO K1 250mx',         price: 161990, oldPrice: 179990, image: '/photos/kayo k1.jpg',           monthly: 14000, credit: 5000,  cashback: 16000 },
                { id: 6, name: 'Мотоцикл BSE Z1',                price: 169990, oldPrice: null,   image: '/photos/bse z1.png',            monthly: 14000, credit: 5000,  cashback: 17000 },
                { id: 7, name: 'Мотоцикл GR-X YX160',            price: 139490, oldPrice: 139490, image: '/photos/grx.png',                 monthly: 11000, credit: 4000,  cashback: 14000 }
            ],

            cartItems: startCart,
            cartOpen: false,

            budget: startBudget,
            budgetInput: startBudget,
            budgetMessage: 'Показаны все мотоциклы',
            budgetMessageColor: 'green',

            form: {
                name: '',
                phone: '',
                email: '',
                message: ''
            },

            sending: false
        }
    },

    computed: {
        filteredMotorcycles() {
            let result = []
            for (let i = 0; i < this.motorcycles.length; i++) {
                if (this.motorcycles[i].price <= this.budget) {
                    result.push(this.motorcycles[i])
                }
            }
            return result
        },

        totalPrice() {
            let total = 0
            for (let i = 0; i < this.cartItems.length; i++) {
                total += this.cartItems[i].price
            }
            return total
        },

        cartCount() {
            return this.cartItems.length
        }
    },

    methods: {
        cartButtonText(moto) {
            if (this.isInCart(moto)) {
                return 'убрать из корзины'
            }
            return 'добавить в корзину'
        },

        applyBudget() {
            let val = parseInt(this.budgetInput)
            if (val < 100000) val = 100000
            if (val > 500000) val = 500000
            this.budgetInput = val
            this.budget = val
            const found = this.filteredMotorcycles.length
            const formatted = val.toLocaleString('ru-RU') + ' ₽'
            if (found === 0) {
                this.budgetMessage = 'Нет мотоциклов до ' + formatted
                this.budgetMessageColor = 'red'
            } else {
                this.budgetMessage = 'Найдено ' + found + ' мотоциклов до ' + formatted
                this.budgetMessageColor = '#3498db'
            }
            localStorage.setItem('moto-budget', val)
        },

        resetBudget() {
            this.budgetInput = 500000
            this.budget = 500000
            this.budgetMessage = 'Показаны все мотоциклы'
            this.budgetMessageColor = 'green'
            localStorage.removeItem('moto-budget')
        },

        isInCart(moto) {
            for (let i = 0; i < this.cartItems.length; i++) {
                if (this.cartItems[i].id === moto.id) return true
            }
            return false
        },

        toggleCart(moto) {
            if (this.isInCart(moto)) {
                for (let i = 0; i < this.cartItems.length; i++) {
                    if (this.cartItems[i].id === moto.id) {
                        this.cartItems.splice(i, 1)
                        break
                    }
                }
            } else {
                this.cartItems.push({ id: moto.id, name: moto.name, price: moto.price })
            }
            localStorage.setItem('motors-cart', JSON.stringify(this.cartItems))
        },

        removeFromCart(index) {
            this.cartItems.splice(index, 1)
            localStorage.setItem('motors-cart', JSON.stringify(this.cartItems))
        },

        openCart()  { this.cartOpen = true  },
        closeCart() { this.cartOpen = false },

        buyNow() {
            if (this.cartItems.length === 0) return
            alert('Спасибо за покупку! Ваш заказ оформлен. С вами свяжется менеджер.')
            this.cartItems = []
            localStorage.removeItem('motors-cart')
            this.closeCart()
        },

        scrollTo(id) {
            document.getElementById(id).scrollIntoView({ behavior: 'smooth' })
        },

        submitForm() {
            if (!this.form.name || !this.form.phone) {
                alert('Заполните имя и телефон')
                return
            }
            this.sending = true
            fetch('', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(this.form)
            })
            .then(response => {
                if (response.status === 201) {
                    alert('Сообщение отправлено!')
                } else {
                    alert('Ошибка отправки. Попробуйте позже.')
                }
            })
            .catch(() => {
                alert('Ошибка отправки. Попробуйте позже.')
            })
            .finally(() => {
                this.sending = false
                this.form = { name: '', phone: '', email: '', message: '' }
            })
        }
    },

}
</script>

<template>
    <Header :currentPage="'home'" @navigate="scrollTo" />

    <div class="cart-button" @click="openCart">
        🛒 <span class="count">{{ cartCount }}</span>
    </div>

    <section id="home" class="hero-section">
        <div class="hero-content">
            <img src="/photos/logo_dark.png" alt="Z-MOTORS" class="hero-logo">
            <h1 class="hero-title">Мототехника<br>для настоящих ценителей</h1>
            <p class="hero-subtitle">Профессиональная техника, выгодные цены, гарантия качества</p>
            <button class="hero-button" @click="scrollTo('katalog')">☰ Перейти в каталог</button>
        </div>
    </section>

    <section class="info-section">
        <div class="info-container">
            <img src="/photos/info_now.png" alt="Акции" class="info-banner">
        </div>
    </section>

    <section class="partners-section">
        <div class="container">
            <h2 class="section-title">Нам доверяют</h2>
            <p class="section-subtitle">
                <span class="highlight">Мы работаем с ведущими компаниями России.</span>
                Являемся ключевыми поставщиками, помогаем в организации массовых мероприятий.
            </p>
            <div class="row g-4 justify-content-center mt-3">
                <div class="col-12 col-md-4">
                    <div class="partner-card p-4 text-center rounded">
                        <img src="/logos/Partner_6.png" alt="Avito" class="partner-logo">
                    </div>
                </div>
                <div class="col-12 col-md-4">
                    <div class="partner-card p-4 text-center rounded">
                        <img src="/logos/yandex_logo.png" alt="Яндекс" class="partner-logo">
                    </div>
                </div>
                <div class="col-12 col-md-4">
                    <div class="partner-card p-4 text-center rounded">
                        <img src="/logos/Partner_9.png" alt="Сбербанк" class="partner-logo">
                    </div>
                </div>
            </div>
        </div>
    </section>

    <section class="features-section">
        <div class="container">
            <div class="feature-card feature-orange">
                <div class="feature-icon">
                    <img src="/photos/favourite.png" alt="Лучшие цены" width="150" height="150">
                </div>
                <div class="feature-content">
                    <h2 class="feature-title">ЛУЧШИЕ ЦЕНЫ,<br>РАССРОЧКА 0%</h2>
                    <p class="feature-text">Гарантируем цены ниже или вернём 110% разницы</p>
                </div>
            </div>
            <div class="feature-card feature-blue">
                <div class="feature-icon">
                    <img src="/photos/videochat.png" alt="Видеоконсультация" width="150" height="150">
                </div>
                <div class="feature-content">
                    <h2 class="feature-title">БЕСПЛАТНАЯ ВИДЕОКОНСУЛЬТАЦИЯ</h2>
                    <p class="feature-text">Получите консультацию по видео прямо сейчас!</p>
                </div>
            </div>
            <div class="feature-card feature-dark">
                <div class="feature-icon">
                    <img src="/photos/S600xU_2x (1).png" alt="Развитие" width="150" height="150">
                </div>
                <div class="feature-content">
                    <h2 class="feature-title">ДВИГАЙТЕСЬ С НАМИ!</h2>
                    <p class="feature-text">В Z-MOTORS работают лучшие в своём деле.</p>
                </div>
            </div>
        </div>
    </section>

    <section id="katalog" class="katalog-section">
        <div class="container">
            <h2 class="katalog-title">Каталог мотоциклов</h2>

            <div class="budget-filter">
                <div class="row align-items-end g-3">
                    <div class="col-12">
                        <h3 class="budget-header-title">🎯 Подберите мотоцикл по бюджету</h3>
                    </div>
                    <div class="col-12 col-md-6 col-lg-4">
                        <label class="budget-label">Ваш бюджет:</label>
                        <div class="budget-input-group">
                            <input
                                type="number"
                                v-model="budgetInput"
                                min="100000"
                                max="500000"
                                step="10000"
                                class="form-control"
                            >
                            <span class="budget-currency">₽</span>
                        </div>
                    </div>
                    <div class="col-12 col-md-6 col-lg-4">
                        <div class="d-flex gap-2">
                            <button @click="applyBudget" class="btn btn-primary">Применить</button>
                            <button @click="resetBudget" class="btn btn-secondary">Сбросить</button>
                        </div>
                    </div>
                    <div class="col-12">
                        <p :style="{ color: budgetMessageColor }">{{ budgetMessage }}</p>
                    </div>
                </div>
            </div>

            <div class="row g-4 mt-2">
                <div
                    v-for="moto in filteredMotorcycles"
                    :key="moto.id"
                    class="col-12 col-md-6 col-lg-4"
                >
                    <div class="card product-card h-100">
                        <img
                            :src="moto.image"
                            :alt="moto.name"
                            class="product-image mx-auto d-block mt-3"
                        >
                        <div class="card-body">
                            <p class="product-name">{{ moto.name }}</p>
                            <p class="product-price">
                                {{ moto.price.toLocaleString('ru-RU') }} ₽
                                <span v-if="moto.oldPrice" class="old-price">
                                    {{ moto.oldPrice.toLocaleString('ru-RU') }}
                                </span>
                            </p>
                            <div class="price-row">
                                <div class="price-box">
                                    <p>в рассрочку<br>
                                        <span class="monthly-price">{{ moto.monthly.toLocaleString('ru-RU') }} ₽/мес.</span>
                                    </p>
                                </div>
                                <div class="price-box">
                                    <p>в кредит<br>
                                        <span class="credit-price">{{ moto.credit.toLocaleString('ru-RU') }} ₽/мес.</span>
                                    </p>
                                </div>
                            </div>
                            <div class="info-box">Вернем {{ moto.cashback.toLocaleString('ru-RU') }} ₽</div>
                            <div class="info-box">Гарантия лучшей цены</div>
                            <button
                                class="cart-add-btn mt-2"
                                :class="{ added: isInCart(moto) }"
                                @click="toggleCart(moto)"
                            >
                                {{ cartButtonText(moto) }}
                            </button>
                        </div>
                    </div>
                </div>
            </div>

            <div v-if="filteredMotorcycles.length === 0" class="text-center py-5">
                <p class="text-white fs-5">😔 Нет мотоциклов в данном ценовом диапазоне</p>
                <button @click="resetBudget" class="btn btn-primary mt-3">Сбросить фильтр</button>
            </div>
        </div>
    </section>

    <section class="promo-section">
        <div class="container">
            <div class="promo-grid">
                <div class="promo-card promo-yellow">
                    <div class="promo-content">
                        <h3 class="promo-title">Не нашли того чего искали?</h3>
                        <p class="promo-text">Напишите нам и мы подберем транспорт для вас!</p>
                        <button class="promo-button" @click="scrollTo('kontakti')">Написать</button>
                    </div>
                    <img src="/photos/helmet.png" alt="шлем" class="promo-image">
                </div>
                <div class="promo-card promo-white">
                    <div class="promo-content">
                        <h3 class="promo-title">Будьте в курсе всех акций!</h3>
                        <p class="promo-text">Подписывайтесь на наши каналы в соцсетях!</p>
                        <img src="/photos/сотсети.png" alt="соцсети" class="social-icons">
                    </div>
                    <img src="/photos/enduro.jpg" alt="эндуро" class="promo-bike">
                </div>
            </div>
        </div>
    </section>

    <section id="kontakti" class="kontakti-section">
        <div class="container">
            <h2 class="kontakti-title">Контакты</h2>
            <div class="row g-4 mb-5">
                <div class="col-12 col-md-6 col-lg-3">
                    <div class="contact-card card-blue p-4 rounded h-100">
                        <h3 class="contact-title">Покупателям</h3>
                        <p class="contact-text">Если у вас вопрос по товару, доставке, оплате или работе наших магазинов</p>
                        <p class="contact-phone-large">+7(232) 423-42-42</p>
                    </div>
                </div>
                <div class="col-12 col-md-6 col-lg-3">
                    <div class="contact-card card-orange p-4 rounded h-100">
                        <h3 class="contact-title">Платный ремонт</h3>
                        <p class="contact-text">Мы ремонтируем технику с двигателем внутреннего сгорания всех производителей</p>
                        <p class="contact-phone-large">+7(378) 423-42-42</p>
                    </div>
                </div>
                <div class="col-12 col-md-6 col-lg-3">
                    <div class="contact-card card-orange p-4 rounded h-100">
                        <h3 class="contact-title">Гарантийный ремонт</h3>
                        <p class="contact-text">Помощь в обслуживании техники, запрос на гарантийный ремонт</p>
                        <p class="contact-phone-large">+7(378) 452-42-42</p>
                    </div>
                </div>
                <div class="col-12 col-md-6 col-lg-3">
                    <div class="contact-card card-blue p-4 rounded h-100">
                        <h3 class="contact-title">Общий телефон</h3>
                        <p class="contact-text">Звоните нам в любое время, мы всегда рады помочь</p>
                        <p class="contact-phone-large">+7(888) 423-42-42</p>
                    </div>
                </div>
            </div>

            <div class="row">
                <div class="col-12 col-lg-8 offset-lg-2">
                    <div class="feedback-container p-5 rounded">
                        <h2 class="feedback-title">Напишите нам</h2>
                        <p class="feedback-description">Есть вопросы или предложения? Оставьте заявку и мы свяжемся с вами.</p>
                        <div class="row g-3 mt-2">
                            <div class="col-12 col-md-6">
                                <input v-model="form.name"    type="text"  placeholder="Ваше имя"     class="form-control">
                            </div>
                            <div class="col-12 col-md-6">
                                <input v-model="form.phone"   type="tel"   placeholder="Ваш телефон"  class="form-control">
                            </div>
                            <div class="col-12">
                                <input v-model="form.email"   type="email" placeholder="Ваш email"    class="form-control">
                            </div>
                            <div class="col-12">
                                <textarea v-model="form.message" placeholder="Ваше сообщение" class="form-control" rows="4"></textarea>
                            </div>
                            <div class="col-12" v-if="form.name">
                                <p class="preview-text">Привет, <strong>{{ form.name }}</strong>! Мы скоро свяжемся с тобой.</p>
                            </div>
                            <div class="col-12">
                                <button class="btn btn-primary w-100" :disabled="sending" @click="submitForm">
                                    {{ sending ? 'Отправка...' : 'Отправить' }}
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <div class="cart-panel" :class="{ open: cartOpen }">
        <div class="cart-header">
            <h2 class="cart-title">Корзина</h2>
            <button class="close-cart" @click="closeCart">&times;</button>
        </div>
        <div class="cart-items">
            <p v-if="cartItems.length === 0" class="empty-cart">Корзина пуста</p>
            <div v-for="(item, index) in cartItems" :key="item.id" class="cart-item">
                <p><b>{{ item.name }}</b></p>
                <p>{{ item.price.toLocaleString('ru-RU') }} ₽</p>
                <button class="btn btn-sm btn-danger" @click="removeFromCart(index)">Удалить</button>
            </div>
        </div>
        <div class="cart-footer">
            <div class="cart-total">
                <span>Итого:</span>
                <span>{{ totalPrice.toLocaleString('ru-RU') }} ₽</span>
            </div>
            <button class="btn btn-success w-100 mt-2" :disabled="cartItems.length === 0" @click="buyNow">
                Купить сейчас
            </button>
        </div>
    </div>
    <div class="overlay" :class="{ active: cartOpen }" @click="closeCart"></div>

    <Footer @navigate="scrollTo" />
</template>

<style scoped>
.hero-section {
    min-height: 80vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(rgba(0,0,0,0.7), rgba(0,0,0,0.7));
    padding: 40px 20px;
    text-align: center;
}
.hero-content  { max-width: 800px; }
.hero-logo     { width: 300px; height: auto; margin-bottom: 30px; }
.hero-title    { color: white; font-size: 48px; font-weight: 800; line-height: 1.2; margin-bottom: 20px; }
.hero-subtitle { color: #bdc3c7; font-size: 20px; margin-bottom: 40px; line-height: 1.6; }
.hero-button   { background: linear-gradient(135deg, #e67e22, #d35400); color: white; border: none; padding: 18px 40px; border-radius: 50px; font-size: 18px; font-weight: 600; cursor: pointer; transition: all 0.3s; }
.hero-button:hover { transform: translateY(-5px); box-shadow: 0 15px 35px rgba(230,126,34,0.4); }

.info-section   { padding: 40px 20px; background: #1a252f; }
.info-container { max-width: 1200px; margin: 0 auto; }
.info-banner    { width: 100%; border-radius: 15px; }

.partners-section  { background: white; padding: 80px 20px; }
.section-title     { color: #2c3e50; font-size: 36px; text-align: center; margin-bottom: 20px; font-weight: 700; }
.section-subtitle  { color: #7f8c8d; text-align: center; font-size: 18px; line-height: 1.6; margin-bottom: 50px; max-width: 800px; margin-left: auto; margin-right: auto; }
.highlight         { color: #e74c3c; font-weight: 600; }
.partner-card      { background: #f8f9fa; border: 2px solid #e8e8e8; transition: all 0.3s; }
.partner-card:hover{ transform: translateY(-10px); border-color: #3498db; }
.partner-logo      { max-width: 100%; height: auto; filter: grayscale(100%); transition: filter 0.3s; }
.partner-card:hover .partner-logo { filter: grayscale(0%); }

.features-section  { padding: 80px 20px; background: linear-gradient(135deg, #f8f9fa, #e8e8e8); }
.feature-card      { display: flex; align-items: center; gap: 50px; margin-bottom: 50px; padding: 50px; border-radius: 20px; transition: all 0.3s; }
.feature-card:hover{ transform: translateY(-10px); box-shadow: 0 20px 50px rgba(0,0,0,0.15); }
.feature-orange    { background: linear-gradient(135deg, #e67e22, #d35400); color: white; }
.feature-blue      { background: linear-gradient(135deg, #3498db, #2980b9); color: white; flex-direction: row-reverse; }
.feature-dark      { background: linear-gradient(135deg, #2c3e50, #1a252f); color: white; }
.feature-icon      { flex-shrink: 0; }
.feature-content   { flex: 1; }
.feature-title     { font-size: 32px; margin-bottom: 20px; font-weight: 700; line-height: 1.3; }
.feature-text      { font-size: 18px; line-height: 1.6; opacity: 0.9; }

.katalog-section   { background-color: rgb(43, 42, 51); padding: 60px 20px; }
.katalog-title     { color: white; font-size: 36px; font-weight: 700; text-align: center; margin-bottom: 40px; }

.budget-filter         { background: white; border-radius: 15px; padding: 30px; margin-bottom: 30px; }
.budget-header-title   { color: #2c3e50; font-size: 20px; margin-bottom: 5px; }
.budget-label          { font-weight: 600; color: #2c3e50; margin-bottom: 6px; display: block; }
.budget-input-group    { display: flex; align-items: center; gap: 8px; }
.budget-currency       { font-size: 18px; font-weight: 600; color: #2c3e50; }

.product-card         { border-radius: 12px; transition: transform 0.2s, box-shadow 0.2s; }
.product-card:hover   { transform: translateY(-5px); box-shadow: 0 10px 30px rgba(0,0,0,0.15); }
.product-image        { max-width: 100%; height: 150px; object-fit: contain; }
.product-name         { font-weight: 600; font-size: 15px; color: #2c3e50; }
.product-price        { font-size: 18px; font-weight: 700; color: #e74c3c; }
.old-price            { font-size: 13px; color: #95a5a6; text-decoration: line-through; margin-left: 5px; }
.price-row            { display: flex; gap: 10px; margin: 10px 0; }
.price-box            { background: #fff3e0; border-radius: 8px; padding: 8px 10px; flex: 1; font-size: 12px; color: #555; }
.monthly-price        { color: #e67e22; font-weight: 700; }
.credit-price         { color: #e67e22; font-weight: 700; }
.info-box             { background: #f0f8ff; border-radius: 6px; padding: 6px 10px; font-size: 12px; color: #2980b9; margin-bottom: 6px; }

.promo-section { padding: 80px 20px; background: white; }
.promo-grid    { display: grid; grid-template-columns: repeat(auto-fit, minmax(400px, 1fr)); gap: 40px; max-width: 1200px; margin: 0 auto; }
.promo-card    { border-radius: 20px; display: flex; align-items: center; padding: 40px; gap: 30px; transition: all 0.3s; box-shadow: 0 10px 30px rgba(0,0,0,0.1); }
.promo-card:hover { transform: translateY(-10px); }
.promo-yellow  { background: linear-gradient(135deg, #f39c12, #e67e22); color: white; }
.promo-white   { background: white; color: #2c3e50; border: 2px solid #e8e8e8; }
.promo-content { flex: 1; }
.promo-title   { font-size: 24px; margin-bottom: 15px; font-weight: 700; }
.promo-text    { line-height: 1.6; margin-bottom: 25px; opacity: 0.9; }
.promo-button  { background: white; color: #e67e22; border: none; padding: 12px 30px; border-radius: 8px; font-weight: 600; cursor: pointer; transition: all 0.3s; }
.promo-image, .promo-bike { width: 200px; height: auto; border-radius: 10px; }
.social-icons  { max-width: 200px; height: auto; }

.kontakti-section     { background: linear-gradient(180deg, #2c3e50, #1a252f); padding: 60px 20px; }
.kontakti-title       { color: white; font-size: 36px; font-weight: 700; text-align: center; margin-bottom: 40px; }
.card-blue            { background: linear-gradient(135deg, #3498db, #2980b9); color: white; }
.card-orange          { background: linear-gradient(135deg, #e67e22, #d35400); color: white; }
.contact-title        { font-size: 18px; font-weight: 700; margin-bottom: 10px; }
.contact-text         { font-size: 14px; line-height: 1.6; opacity: 0.9; }
.contact-phone-large  { font-size: 20px; font-weight: 700; margin-top: 15px; }
.feedback-container   { background: white; }
.feedback-title       { font-size: 28px; font-weight: 700; color: #2c3e50; }
.feedback-description { color: #7f8c8d; }
.preview-text         { color: #3498db; font-size: 14px; background: #ebf5fb; padding: 10px 15px; border-radius: 8px; border-left: 4px solid #3498db; }

.cart-item .btn { transform: none !important; transition: none !important; }
.cart-footer .btn { transform: none !important; transition: none !important; }

.cart-add-btn { background: #27ae60; color: white; border: none; padding: 12px 20px; border-radius: 6px; cursor: pointer; font-size: 15px; transition: all 0.3s; width: 100%; margin-top: 15px; font-weight: 600; text-transform: uppercase; }
.cart-add-btn:hover { background: #219653; transform: translateY(-2px); }
.cart-add-btn.added { background: #e74c3c; }
.cart-add-btn.added:hover { background: #c0392b; }
.cart-button { position: fixed; top: 60px; right: 20px; background: #2c3e50; color: white; border-radius: 50%; width: 60px; height: 60px; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 24px; z-index: 99; border: 3px solid white; box-shadow: 0 4px 15px rgba(0,0,0,0.2); transition: all 0.3s; }
.cart-button:hover { background: #3498db; transform: scale(1.1); }
.count { position: absolute; top: -8px; right: -8px; background: #e74c3c; color: white; border-radius: 50%; width: 26px; height: 26px; display: flex; align-items: center; justify-content: center; font-size: 13px; font-weight: bold; border: 2px solid white; }

.cart-panel        { position: fixed; top: 0; right: -400px; width: 380px; height: 100vh; background: white; z-index: 1000; transition: right 0.3s ease; display: flex; flex-direction: column; box-shadow: -5px 0 20px rgba(0,0,0,0.2); }
.cart-panel.open   { right: 0; }
.cart-header       { display: flex; justify-content: space-between; align-items: center; padding: 20px; border-bottom: 1px solid #e8e8e8; }
.cart-title        { margin: 0; font-size: 20px; }
.close-cart        { background: none; border: none; font-size: 28px; cursor: pointer; }
.cart-items        { flex: 1; overflow-y: auto; padding: 20px; }
.cart-item         { border-bottom: 1px solid #f0f0f0; padding: 10px 0; }
.empty-cart        { color: #95a5a6; text-align: center; margin-top: 40px; }
.cart-footer       { padding: 20px; border-top: 1px solid #e8e8e8; }
.cart-total        { display: flex; justify-content: space-between; font-weight: 700; font-size: 18px; margin-bottom: 10px; }

.overlay        { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 999; }
.overlay.active { display: block; }

@media (max-width: 992px) {
    .hero-title   { font-size: 36px; }
    .feature-card { flex-direction: column; text-align: center; gap: 30px; padding: 40px 30px; }
    .feature-blue { flex-direction: column; }
    .promo-grid   { grid-template-columns: 1fr; }
    .promo-card   { flex-direction: column; text-align: center; }
}
@media (max-width: 768px) {
    .hero-title  { font-size: 28px; }
    .hero-button { padding: 15px 30px; font-size: 16px; }
    .feature-title { font-size: 24px; }
}
</style>