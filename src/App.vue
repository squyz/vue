<template>
    <div class="app">
        <h1>Страница с постами</h1>

        <my-input v-model="searchQuery"/>

        <div class="app__btns">
            <my-button
                @click="showDialog"
            >
            Создать пост
            </my-button>
            <my-select
                v-model='selectedSort'
                :options="sortOptions"
            />
        </div>

        <my-dialog v-model:show="dialogVisible">
            <post-form
            @create="createPost"
            />
        </my-dialog>

        <post-list 
            :posts="sortedAndSearchedPosts"
            @remove = 'removePost'
            v-if="!isPostLoading"
        />

        <div v-else>
            Идет загрузка...
        </div>

        <test
            v-model:input="sayHellos"
        />

        <div class="observer" ref="observer"></div>
    </div>
</template>

<script>
import PostForm from '@/components/PostForm';
import PostList from '@/components/PostList';
import Test from '@/components/Test';
import axios from 'axios';

export default {
    components: {
    PostList, PostForm,
    Test
    },
    data() {
        return {
            posts: [],
            dialogVisible: false,
            isPostLoading: false,
            selectedSort: '',
            searchQuery: '',
            page: 1,
            limit: 10,
            totalPages: 1,
            sortOptions: [
                {value: 'title', name: 'По названию'},
                {value: 'body', name: 'По описанию'},
                {value: 'id', name: 'По дате'},
            ],
            sayHellos: 'hello',
        }
    },
    methods: {
        createPost (post) {
            this.posts.push(post);
            this.dialogVisible = false
        },
        sayHello(words){
            console.log(words)
        },
        removePost(post){
            this.posts = this.posts.filter(p => p.id !== post.id)
        },
        showDialog(){
            this.dialogVisible = true
        },
        async fetchFunc(){
            try{
                this.isPostLoading = true;
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts?', {
                    params: {
                        _page: this.page,
                        _limit: this.limit,
                    }
                });
                this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit);
                this.posts = response.data;
            }
            catch{
                alert('Ошибка')
            }
            finally{
                this.isPostLoading = false;
            }
        },

        async loadMorePosts(){
            this.page += 1;
            try{
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts?', {
                    params: {
                        _page: this.page,
                        _limit: this.limit,
                    }
                });
                this.posts = [...this.posts, ...response.data];
            }
            catch{
                alert('Ошибка')
            }
        },
    },
    mounted(){
        this.fetchFunc();

        const options = {
            rootMargin: "0px",
            threshold: 1.0,
        };
        
        const callback = entries => {
            if(entries[0].isIntersecting && this.page < this.totalPages){
                this.loadMorePosts();
            }
        }
        const observer = new IntersectionObserver(callback, options);
        observer.observe(this.$refs.observer)
    },
    computed: {
        sortedPosts(){
            if(this.selectedSort === 'id'){
                return [...this.posts].sort((a, b) => {return a[this.selectedSort] - b[this.selectedSort]})
            }
            else{
                return [...this.posts.sort((a, b) => {return a[this.selectedSort]?.localeCompare(b[this.selectedSort])})]
            }
        },
        sortedAndSearchedPosts(){
            return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
        }
    },
    watch: {
    }
}
</script>

<style>
*{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
.app{
    padding: 20px;
}
.app__btns{
    margin-top: 12px;
    display: flex;
    justify-content: space-between;
}
.page__wrapper{
    display: flex;
    margin-top: 15px;
}
.page{
    border: 1px solid black;
    padding: 10px;
}
.current-page{
    border: 2px solid teal;
}

.observer{
    width: 100%;
    height: 24px;
    background-color: black;
}
</style>