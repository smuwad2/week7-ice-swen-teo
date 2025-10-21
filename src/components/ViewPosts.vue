<script setup>
    import axios from 'axios';
</script>

<script>
export default {
    data() {
        return {
            moods: ["Happy", "Sad", "Angry"],
            posts: [], // array of post objects
            entry: "",
            mood: "",
            showEditPost: false,
            editPostId: "",
        }
    },
    computed: {
        baseUrl() {
            if (window.location.hostname=='localhost')
                return 'http://localhost:3000' 
            else {
                const codespace_host = window.location.hostname.replace('5173', '3000')
                return `https://${codespace_host}`;
            }
        }
    },
    created() { // created is a hook that executes as soon as Vue instance is created
        axios.get(`${this.baseUrl}/posts`)
            .then(response => {
                // this gets the data, which is an array, and pass the data to Vue instance's posts property
                this.posts = response.data
            })
            .catch(error => {
                this.posts = [{ entry: 'There was an error: ' + error.message }]
            })
    },
    methods: {
        editPost(id) {
            // 1. Find the post that was clicked
            const postToEdit = this.posts.find(item => item.id === id);

            // 2. Populate the form with that post's data
            this.entry = postToEdit.entry;
            this.mood = postToEdit.mood;
            
            // 3. Store the id so 'updatePost' knows which post to update
            this.editPostId = id;

            // 4. Show the edit form
            this.showEditPost = true;
        },
        updatePost(event) {
            // 1. Send the updated data to the server
            // We send the id as a query parameter, just as index.js expects
            axios.post(`${this.baseUrl}/updatePost?id=${this.editPostId}`, {
                entry: this.entry.trim(),
                mood: this.mood.trim()
            })
            .then(response => {
                console.log(response.data); // Log success message
                
                // 2. Hide the edit form
                this.showEditPost = false;

                // 3. Re-fetch all posts to update the table with the new data
                axios.get(`${this.baseUrl}/posts`)
                    .then(response => {
                        this.posts = response.data;
                    })
                    .catch(error => {
                        console.log(error.message);
                    });
            })
            .catch(error => {
                console.log(error.message);
            });
        }
    }
}
</script>

<template>
    <div id="demo">
        <h2> View Blog Posts </h2>
        <table class="table m-2">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Entry</th>
                    <th>Mood</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="post in posts">
                    <td>{{ post.id }}</td>
                    <td>{{ post.entry }}</td>
                    <td>{{ post.mood }}</td>
                    <td><button @click="editPost(post.id)">Edit</button></td>
                </tr>
            </tbody>

        </table>
        
        <div id="editPost" v-if="showEditPost">
            <h3>Edit Post</h3>
            <div id="postContent" class="mx-3">
                <form @submit.prevent="updatePost">
                    <div class="mb-3">
                        <label for="entry" class="form-label">Entry</label>
                        <textarea id="entry" class="form-control" v-model="entry" required></textarea>
                    </div>
                    <div class="mb-3">
                        <label for="mood" class="form-label">Mood</label>
                        <select id="mood" class="form-select" v-model="mood" required>
                            <option value="" disabled>Select Mood</option>
                            <option v-for="mood in moods" :value="mood">{{ mood }}</option>
                        </select>
                    </div>
                    <button type="submit" class="btn btn-primary">Update Post</button>
                </form>
            </div>
        </div>
    </div>
</template>

<style scoped></style>
