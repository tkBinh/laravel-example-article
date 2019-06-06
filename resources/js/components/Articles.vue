<template>
    <div>
        <h1>Article List Post</h1>
        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item">
                    <a class="page-link" href="#" @click="fetchArticle(pagination.prev_page_url)">Previous</a>
                </li>
                <li class="page-item disabled">
                    <a href="#" class="page-link text-dark">Page {{ pagination.current_page }} of {{
                        pagination.last_page }}</a>
                </li>
                <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item">
                    <a class="page-link" href="#" @click="fetchArticle(pagination.next_page_url)">Next</a>
                </li>
            </ul>
        </nav>
        <div v-for="article in articles" v-bind:key="article.id" class="card card-body mb-2">
            <h3>{{ article.title }}</h3>
            <p>{{ article.body }}</p>
            <hr>
            <button class="btn btn-danger col-5" @click="deleteArticle(article.id)">Delete</button>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                articles: [],
                article: {id: '', title: '', body: ''},
                article_id: '',
                pagination: {},
                edit: false
            }
        },
        created() {
            this.fetchArticle();
        },
        methods: {
            fetchArticle(page_url) {
                let vm = this;
                page_url = page_url || '/api/articles'
                fetch(page_url)
                    .then(response => response.json())
                    .then(response => {
                        this.articles = response.data;
                        vm.makePagination(response.meta, response.links);
                    }).catch(err => console.log(err));
            },
            makePagination(meta, links) {
                let pagination = {
                    current_page: meta.current_page,
                    last_page: meta.last_page,
                    next_page_url: links.next,
                    prev_page_url: links.prev,
                }
                this.pagination = pagination;
            },
            deleteArticle(id) {
                if (confirm('Are you sure ?')) {
                    fetch("/article/${id}", {
                        method: 'delete'
                    })
                        .then(response => response.json())
                        .then(data => {
                        alert('Removed !!!');
                        this.fetchArticle();
                    }).catch(err => console.log(err));
                }
            }
        }
    }
</script>

<style scoped>

</style>