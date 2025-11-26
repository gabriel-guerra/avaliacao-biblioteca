<script setup>
import { ref } from 'vue';
import { string, number } from 'yup';
import { useForm } from 'vee-validate';
import { useToast } from 'primevue/usetoast';
import router from '../router/index'

const toast = useToast();

const schema = {

    texto: string().min(0).max(100).required().label('Texto'),
    numero: number().min(0).max(100).required().label('Numero')
};

const {

    defineField,
    handleSubmit,
    resetForm
    
} = useForm({ validationSchema: schema });

const [texto] = defineField('texto');
const [numero] = defineField('numero');

const onSubmit = handleSubmit((values) => {

    log(values);

    toast.add({
        severity: 'success',
        summary: 'Sucesso',
        detail: 'Informações salvas com sucesso!',
        life: 3000,
    });

}, (error) => {

    log(error)

    toast.add({
        severity: 'error',
        summary: 'Erro',
        detail: 'Não foi possível salvar!',
        life: 3000,
    });
});

function goToPage(page) {
    router.push(`/${page}`)
}

</script>
<template>
    <div class="w-full">
        <div class="flex justify-center text-2xl">
            <span>Home</span>
        </div>

        <div class="card mt-10">
            <div class="title">Lista de livros</div>
            <div class="mt-10">
                <button @click="goToPage('livros')">Ir para página</button>
            </div>
        </div>

    </div>
</template>