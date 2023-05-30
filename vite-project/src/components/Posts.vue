<template>
    <div>
        <h1>Posts</h1>
        <input type="text" v-model="title" placeholder="Title" class="title-input" />
        <input type="text" v-model="body" placeholder="Body" class="body-input" />
        <button v-if="is_editing" @click="update_post">UPDATE</button>
        <button v-if="is_editing" @click="cancel_edit">CANCEL</button>

        <button v-else @click="create_post">CREATE</button>

        <!-- list of post-->
        <div v-for="post in posts" :key="post.id">
            <h3>{{ post.title }}</h3>
            <p>{{ post.body }}</p>
            <button @click="edit_post(post.id)">EDIT</button>
            <button @click="delete_post(post.id)">DELETE</button>
        </div>
    </div>
</template>

<script setup>
    import { ref, onMounted } from 'vue';

    const posts = ref([]);
    const title = ref('');
    const body = ref('');
    const is_editing = ref(false);
    const post_id = ref(null);
    const API_URL = 'http://localhost:3000/posts';

    onMounted(async() => {
        const res = await fetch(API_URL);
        posts.value = await res.json();
    });

    

    const update_post = async() => {
        const res = await fetch(`${API_URL}/${post_id.value}`, {
            method: 'PUT',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                title: title.value,
                body: body.value,
                id: post_id.value
            })
        });
        const data = await res.json();
        const index = posts.value.findIndex(post => post.id === post_id.value);
        posts.value[index] = data;

        title.value = '';
        body.value = '';
        post_id.value = 0;
        is_editing.value = false;
    }
    const cancel_edit = async() => {
        title.value = '';
        body.value = '';
        post_id.value = 0;
        is_editing.value = false;
    }
    const create_post = async() => {
        const res = await fetch(API_URL, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                title: title.value,
                body: body.value
            })
        });
        const data = await res.json();
        posts.value.push(data);
        title.value = '';
        body.value = '';
        post_id.value = 0;
    }

    const delete_post = async(id) => {
        const res = await fetch(`${API_URL}/${id}`, {
            method: 'DELETE'
        });
        posts.value = posts.value.filter(post => post.id !== id);
    }

    const edit_post = async(id) => {
        const post = posts.value.find(post => post.id === id);
        
        title.value = post.title;
        body.value = post.body;
        post_id.value = post.id;        
        is_editing.value = true;

        window.scrollTo({
            top: 0,
            behavior: 'smooth'
        });
    }

    
</script>

<style scoped>
    .title-input {
        width: 100%;
        padding: 12px 20px;
        margin: 8px 0;
        box-sizing: border-box;
        border: 2px solid #ccc;
        background-color: #f8f8f8;
        color: #111;
        border-radius: 4px;
        resize: vertical;
    }
    .body-input {
        width: 100%;
        padding: 12px 20px;
        margin: 8px 0;
        box-sizing: border-box;
        border: 2px solid #ccc;
        background-color: #f8f8f8;
        color: #111;
        border-radius: 4px;
        resize: vertical;
    }
</style>