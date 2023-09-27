<template>
    <div class="app">
        <h1>Страница с постами</h1>

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
            :posts="posts"
            @remove = 'removePost'
            v-if="!isPostLoading"
        />

        <div v-else>
            Идет загрузка...
        </div>

        <test
            v-model:input="sayHellos"
        />
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
            sortOptions: [
                {value: 'title', name: 'По названию'},
                {value: 'body', name: 'По описанию'},
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
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts?_limit=10');
                this.posts = response.data;
            }
            catch{
                alert('Ошибка')
            }
            finally{
                this.isPostLoading = false;
            }
        }
    },
    mounted(){
        this.fetchFunc();
    },
    watch: {
        selectedSort(newValue) {
            this.posts.sort((a, b) => {
                return a[newValue]?.localeCompare(b[newValue])
            })
        }
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
</style>