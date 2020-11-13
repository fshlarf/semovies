<template>
    <div class="login">
        <div class="login-form mb-2">
            <label for="username">Username</label>
            <input v-model="user.username" @keypress="handleInput('username')" id="username" type="text" class="border rounded border-gray-400 mt-1 mb-2 py-2 px-2" placeholder="Example: hello">
            <p class="text-red-600" v-if="errorMsg.username">{{ errorMsg.username }}</p>
        </div>
        <div class="login-form mb-2">
            <label for="password">Password</label>
            <input v-model="user.password" @keypress="handleInput('password')" id="password" type="password" class="border rounded border-gray-400 mt-1 mb-2 py-2 px-2" placeholder="Example: hello123">
            <p class="text-red-600" v-if="errorMsg.password">{{ errorMsg.password }}</p>
        </div>
        <p class="text-blue-500">INFO</p>
        <p class="text-blue-500 mb-6">username: hello, password: hello123</p>
        <Button
            btnTitle="Login"
            @btn-click="clickLogin"
        />
    </div>
</template>

<script>
import { defineComponent, reactive } from '@vue/composition-api'
import Button from "~/components/atoms/Button"

export default defineComponent({
    setup(props, { emit }) {
        const dummyUser = {username: 'hello',password: 'hello123'}

        const user = reactive({
            username: '',
            password: ''
        })

        const errorMsg = reactive({
            username: '',
            password: ''
        })

        const clickLogin = () => {
            validateInput()
            if (user.username == dummyUser.username && user.password == dummyUser.password) {
                emit('click-login', user)
            }
        }

        const validateInput = () => {
            if (user.username == '') {
                errorMsg.username = 'Username is required'
                return
            }
            if (user.password == '') {
                errorMsg.password = 'Password is required'
                return
            }
            if (user.username != dummyUser.username) {
                errorMsg.username = 'Username is not registered'
                return
            }
            if (user.password != dummyUser.password) {
                errorMsg.password = 'Password is wrong'
                return
            }
        }

        const handleInput = (type) => {
            type == 'username' ? errorMsg.username = '' : errorMsg.password = ''
        }

        return {
            user,
            errorMsg,
            clickLogin,
            handleInput
        }
    }
})
</script>

<style lang="scss" scoped>
.login {
    margin: 0 auto;
    padding: 10rem 10rem;
    max-width: 640px;
    &-form {
        display: block;
        label {
            width: 100%;
        }
        input {
            width: 100%;
        }
    }
}
@media (max-width: 640px) {
    .login {
        margin: 0 auto;
        padding: 10rem 16px;
        &-form {
            display: block;
            label {
                width: 100%;
            }
            input {
                width: 100%;
            }
        }
    }
}
</style>