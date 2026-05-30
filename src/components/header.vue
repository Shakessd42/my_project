<script>
export default {
    name: 'Header',

    props: {
        currentPage: {
            type: String,
            required: true
        }
    },

    methods: {
        goTo(id) {
            this.$emit('navigate', id)
        }
    }
}
</script>

<template>
<header class="site-header">
    <a @click.prevent="goTo('home')"     href="#" class="header-link">На главную</a>
    <a @click.prevent="goTo('katalog')"  href="#" class="header-link">Каталог</a>
    <a @click.prevent="goTo('kontakti')" href="#" class="header-link">Контакты</a>
    <p class="phone">+7(888) 423-42-42</p>
</header>
</template>

<style scoped>
.site-header {
    display: flex;
    background-color: rgb(14, 66, 114);
    gap: 50px;
    color: white;
    height: 30px;
    align-items: center;
    justify-content: flex-end;
    padding: 10px;
    font-size: 13px;
    position: sticky;
    top: 0;
    z-index: 100;
}
.site-header a { color: whitesmoke; text-decoration: none; cursor: pointer; }
.site-header a:hover { color: #3498db; }
.phone { color: white; margin: 0; }
</style>
