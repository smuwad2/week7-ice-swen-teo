<script>
export default {
    // Add code in appropriate places
    data() {
        return {
            items: ['keyboard', 'mouse', 'iPhone', 'macbook', 'adapter'],
            addeditem: ''
        }

    },
    methods: {
        deleteItem(index){
            this.items.splice(index, 1);
        },
        addItem(){
            this.items.push(this.addeditem);
            this.addeditem = '';
        }
    }
}
</script>

<template>

    <h2>Shopping Cart</h2>
    <!-- TODO: Add Code Here -->
    <ul>
        <li v-for="(item, index) in items" :key="item">
            <span>{{ item }}</span>
            <button @click="deleteItem(index)">Delete!</button>
        </li>
    </ul>
    <input v-model="addeditem"></input><span><button @click="addItem">Add!</button></span>
</template>
