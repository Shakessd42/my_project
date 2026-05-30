<script>
export default {
    name: 'Footer',
    data() {
        return {
            sections: [
                {
                    id: 1,
                    title: 'О компании',
                    links: [
                        { id: 1, text: 'О нас' },
                        { id: 2, text: 'Миссия компании' },
                        { id: 3, text: 'Политика конфиденциальности' },
                        { id: 4, text: 'Видеообзоры Z-MOTORS' },
                        { id: 5, text: 'Видеоотзывы Z-MOTORS' },
                        { id: 6, text: 'Отзывы об Z-MOTORS' },
                        { id: 7, text: 'Карьера в Z-MOTORS' },
                        { id: 8, text: 'Новости' },
                        { id: 9, text: 'Статьи' }
                    ]
                },
                {
                    id: 2,
                    title: 'Покупателям',
                    links: [
                        { id: 1, text: 'Акции и скидки' },
                        { id: 2, text: 'Доставка и оплата' },
                        { id: 3, text: 'Рассрочка и Кредит'},
                        { id: 4, text: 'Правила представления информации' }
                    ]
                },
                {
                    id: 3,
                    title: 'Сервис',
                    links: [
                        { id: 1, text: 'Техническое обслуживание' },
                        { id: 2, text: 'Гарантийный ремонт' },
                        { id: 3, text: 'Платный ремонт' }
                    ]
                },
                {
                    id: 4,
                    title: 'Контакты',
                    links: [
                        { id: 1, text: 'Письмо директору'},
                        { id: 2, text: 'Реквизиты'}
                    ]
                }
            ]
        }
    },

    methods: {
        goTo404() {
            this.$emit('navigate', '404')
        }
    }
}
</script>

<template>
<footer class="site-footer">
    <hr class="footer-line">
    <div class="footer-info">
        <div
            v-for="section in sections"
            :key="section.id"
            class="footer-section"
        >
            <p class="section-title">{{ section.title }}</p>
            <ul class="footer-links">
                <li v-for="link in section.links" :key="link.id">
                    <a href="#" @click.prevent="goTo404">{{ link.text }}</a>
                </li>
            </ul>
            <div v-if="section.id === 4" class="footer-contact">
                <p class="contact-phone">
                    телефон для связи<br>
                    <strong>+7(888) 423-42-42</strong>
                </p>
            </div>
        </div>
    </div>
</footer>
</template>

<style scoped>
.site-footer {
    background: rgb(28, 27, 34);
    color: white;
    padding: 40px 20px;
    margin-top: 50px;
}

.footer-line {
    border: none;
    height: 1px;
    background: rgba(255,255,255,0.1);
    margin: 30px 0;
}

.footer-info {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    gap: 30px;
    max-width: 1400px;
    margin: 0 auto;
}

.footer-section { flex: 1; min-width: 200px; }

.section-title {
    color: #3498db;
    font-weight: 600;
    margin-bottom: 20px;
}

.footer-links { list-style: none; padding: 0; margin: 0; }
.footer-links li { margin-bottom: 10px; }

.footer-links a {
    color: #bdc3c7;
    text-decoration: none;
    transition: 0.3s;
}

.footer-links a:hover {
    color: white;
    padding-left: 5px;
}

.footer-contact {
    background: rgba(255,255,255,0.05);
    border-radius: 8px;
    padding: 20px;
    margin-top: 20px;
    border: 1px solid rgba(255,255,255,0.1);
}

.contact-phone { color: white; line-height: 1.6; }
</style>
