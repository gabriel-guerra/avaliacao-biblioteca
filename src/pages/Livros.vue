<style>

.right{
  display: flex; 
  justify-content: right;
  margin-bottom: 2%;
}

.space{
  margin-right: 2%;
}

.font{
  display: flex;
  font-size: 3rem;
  align-items: center;
}

.margin{
    margin: 2%;
}

@media (max-width: 640px) {
  .right {
    display: flex; 
    justify-content: start;
  }
}

.button{
  border: 1px solid;
  padding: 2%;
  width: 100%;
  border-radius: 20px 20px 20px 20px;
  margin: 5px;
}

.flex{
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.newBook{
  border: 1px solid;
  margin: 5px;
  border-radius: 20px 20px 20px 20px;
  padding: 0px 5px;
}

</style>


<template>
  <Disclosure as="nav" class="relative bg-gray-800/50 after:pointer-events-none after:absolute after:inset-x-0 after:bottom-0 after:h-px after:bg-white/10" v-slot="{ open }">
    <div class="mx-auto max-w-7xl px-2 sm:px-6 lg:px-8">
        <div class="flex flex-1 items-center justify-center sm:items-stretch sm:justify-start">
          <div class="flex shrink-0 items-center margin">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 16 16" fill="white">
                <path d="M1 2.828c.885-.37 2.154-.769 3.388-.893 1.33-.134 2.458.063 3.112.752v9.746c-.935-.53-2.12-.603-3.213-.493-1.18.12-2.37.461-3.287.811zm7.5-.141c.654-.689 1.782-.886 3.112-.752 1.234.124 2.503.523 3.388.893v9.923c-.918-.35-2.107-.692-3.287-.81-1.094-.111-2.278-.039-3.213.492zM8 1.783C7.015.936 5.587.81 4.287.94c-1.514.153-3.042.672-3.994 1.105A.5.5 0 0 0 0 2.5v11a.5.5 0 0 0 .707.455c.882-.4 2.303-.881 3.68-1.02 1.409-.142 2.59.087 3.223.877a.5.5 0 0 0 .78 0c.633-.79 1.814-1.019 3.222-.877 1.378.139 2.8.62 3.681 1.02A.5.5 0 0 0 16 13.5v-11a.5.5 0 0 0-.293-.455c-.952-.433-2.48-.952-3.994-1.105C10.413.809 8.985.936 8 1.783"/>
            </svg>
          </div>
          <input v-model="search" class="margin" type="text" placeholder="Buscar título ou autor" style="width: 100%; padding-left: 1%;">
      </div>
    </div>
  </Disclosure>

  <div class="bg-white">
    <div class="mx-auto max-w-2xl px-4 pt-4 pb-16 sm:px-6 sm:pt-4 sm:pb-24 lg:max-w-7xl lg:px-8">
      <h2 class="font">Lista de livros</h2>
      <div class="flex">
        <button class="newBook" @click="router.push(`/livros/novo`)">Novo livro</button>
        <div class="right">
          <label class="space">Ordenar</label>
          <select name="orderBy" id="orderBy" v-model="orderBy">
            <option value="id">Id</option>
            <option value="authorId">Nome do Autor</option>
            <option value="year">Ano de publicação</option>
          </select>
          <select name="orderType" id="orderType" v-model="orderType">
            <option value="ascending">Crescente</option>
            <option value="descending">Decrescente</option>
          </select>
        </div>
      </div>

      <div class="grid grid-cols-1 gap-x-6 gap-y-10 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 xl:gap-x-8">
        <a v-for="book in bookList" :key="book.id" class="group">
          <img :src="book.cover" class="cursor-pointer aspect-square w-full rounded-lg bg-gray-200 object-cover group-hover:opacity-75 xl:aspect-7/8" @click.prevent="openBook(book)"/>
          <h3 class="mt-4 text-sm text-gray-700">{{ book.name }}</h3>
          <p class="mt-1 text-lg"><span class="font-medium text-gray-900">Id: </span>{{ book.id }}</p>
          <p class="mt-1 text-lg"><span class="font-medium text-gray-900">Título: </span>{{ book.title }}</p>
          <p class="mt-1 text-lg"><span class="font-medium text-gray-900">Autor: </span>{{ getAuthorName(book.authorId) }}</p>
          <p class="mt-1 text-lg"><span class="font-medium text-gray-900">Ano de publicação: </span>{{ book.year }}</p>
          <p class="mt-1 text-lg"><span class="font-medium text-gray-900">Editora: </span>{{ getPublisherName(book.publisherId) }}</p>
          <label for="" style="margin-top: 25%;">Mais Detalhes</label>
          <div class="flex">
            <button class="button" @click="router.push(`/livros/${book.id}`)">Livro</button>
            <button class="button">Autor</button>
            <button class="button">Editora</button>
          </div>
        </a>
      </div>
    </div>
  </div>

  <TransitionRoot as="template" :show="openModal && selectedBook != null">
    <Dialog class="relative z-10" @close="openModal = false">

      <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0" enter-to="opacity-100" leave="ease-in duration-200" leave-from="opacity-100" leave-to="opacity-0" >
        <div class="fixed inset-0 bg-gray-900/50 transition-opacity"></div>
      </TransitionChild>

      <div class="fixed inset-0 flex items-center justify-center overflow-y-auto p-4">
        <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0 translate-y-4 scale-95" enter-to="opacity-100 translate-y-0 scale-100" leave="ease-in duration-200" leave-from="opacity-100 translate-y-0 scale-100" leave-to="opacity-0 translate-y-4 scale-95" >
          <DialogPanel class="bg-white rounded-lg shadow-xl w-full max-w-3xl p-6">
            <div class="mx-auto mt-6 max-w-2xl">
              <img :src="selectedBook.cover" class="rounded-lg object-cover w-full max-w-sm mx-auto" />
            </div>

            <div class="mx-auto max-w-2xl pt-10 pb-4">
              <h1 class="text-3xl font-bold">{{ selectedBook.title }}</h1>

              <p class="mt-2 text-gray-700 text-lg">
                <span class="font-medium">Autor:</span> {{ selectedBook.author }}
              </p>

              <p class="text-gray-700 text-lg">
                <span class="font-medium">Editora:</span> {{ selectedBook.publisher }}
              </p>

              <p class="text-gray-700 text-lg">
                <span class="font-medium">Ano:</span> {{ selectedBook.year }}
              </p>
              
              <p class="text-gray-700 text-lg">
                <span class="font-medium">Sinopse:</span> {{ selectedBook.sinopsis }}
              </p>

            </div>

          </DialogPanel>
        </TransitionChild>
      </div>

    </Dialog>
  </TransitionRoot>

</template>

<script setup>
import router from '../router/index'
import { Dialog, DialogPanel, TransitionChild, TransitionRoot } from '@headlessui/vue'
import { Disclosure } from '@headlessui/vue'
import data from '../data.json' with { type: 'json' };
import { ref, computed } from 'vue'

const orderBy = ref('id')
const orderType = ref('ascending')
const search = ref('')
const openModal = ref(false)
const selectedBook = ref(null)

function openBook(book) {
  selectedBook.value = book
  openModal.value = true
}

const bookList = computed(() => {
  if (orderBy.value === 'none') return data.books
  const field = orderBy.value
  let books; 

  if (field === 'authorId'){
    books = [...data.books].sort((a, b) => {
      if (getAuthorName(a[field]) < getAuthorName(b[field])) return orderType.value === 'ascending' ? -1 : 1
      if (getAuthorName(a[field]) > getAuthorName(b[field])) return orderType.value === 'ascending' ? 1 : -1
      return 0
    })
  }else{
    books = [...data.books].sort((a, b) => {
      if (a[field] < b[field]) return orderType.value === 'ascending' ? -1 : 1
      if (a[field] > b[field]) return orderType.value === 'ascending' ? 1 : -1
      return 0
    })
  }

  if (search.value.trim() !== "") {
    const value = search.value.toLocaleLowerCase()

    return books.filter((book) =>{
      const title =  book.title.toLowerCase().includes(value)
      const author = getAuthorName(book.authorId).toLowerCase().includes(value)
      const publisher = getPublisherName(book.publisherId).toLowerCase().includes(value)

      return title || author || publisher
    })
  }

  return books

})

function getAuthors(){
    return data.authors
}

function getPublishers(){
    return data.publishers
}

function getAuthorName(authorId){
    const author = getAuthors().filter((author) => author.id === authorId)
    return author[0].name
}

function getPublisherName(publisherId){
    const publisher = getPublishers().filter((publisher) => publisher.id === publisherId)
    return publisher[0].name
}

</script>