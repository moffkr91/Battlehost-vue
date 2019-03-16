<template>
    <div id="signup">

        <section class="title">
            <p>Sign Up</p>
        </section>

    <form id="signup-form">
        Email:
        <input 
            type="email" 
            v-model="email"
            @input="validateEmail" 
        />

        Username:
        <input 
            type="text" 
            @input="validateUsername"
            v-model.trim="username" 
        />

        Password:
        <input 
            type="password"
            @input="validatePassword"
            v-model.trim="password"  
        />  

        Confirm Password:
        <input 
            type="password"
            @input="validatePassword2"
            v-model="password2" 
        />

        <div id="signup-submit">   
            <section>
                <button
                    type="submit" 
                    :disabled="invalidForm"
                    v-on:click="submitSignin">
                    Join
                </button>
            </section>
        </div>
    </form>
        <div id="validation-errors">
            <section>
                <transition name="fade">
                    <div id="invalid-email"  v-show="invalidEmail">
                            - Invalid email address.
                    </div>
                </transition>

                <transition name="fade">
                    <div id="invalid-username" v-show="invalidUsername">
                            - {{invalidUsernameErr}}
                    </div>
                </transition>

                <transition name="fade">
                    <div id="unconfirmed-password" v-show="invalidPassword2">
                            - Passwords must match.
                    </div>
                </transition>

                <transition name="fade">
                    <div id="invalid-password" v-show="invalidPassword">
                            - Passwords must contain:
                            <ul>
                                <transition name="fade"><li v-show="passLengthErr">Between 6 and 16 characters total characters.</li></transition>
                                <transition name="fade"><li v-show="passLowerErr">One lower case letter.</li></transition>
                                <transition name="fade"><li v-show="passUpperErr">One upper case letter.</li></transition>
                                <transition name="fade"><li v-show="passNumberErr">One number.</li></transition>
                                <transition name="fade"><li v-show="passSpecialErr">One special character</li></transition>
                            </ul>
                    </div>
                </transition>

            </section>
        </div>

    <div class="back-btns">
        <section>
            <button 
                @click="backClick">
                Back
            </button>
        </section>
    </div> <!-- signup-buttons -->

    </div> <!-- signup -->
</template>

<script>
import axios from 'axios'

export default {
    name: 'Signup',
    data: () => ({
        email: '',
        username: '',
        password: '',
        password2: '',
        invalidEmail: '',
        invalidUsername: '',
        invalidPassword: '',
        invalidPassword2: '',
        invalidUsernameErr: '',
        emailErr: false,
        passLengthErr: false,
        passLowerErr: false,
        passUpperErr: false,
        passNumberErr: false,
        passSpecialErr: false,
        invalidForm: true

    }),
    methods: {
        backClick() {
            this.$emit("welcomeClick")
        },
        submitSignin() {
            event.preventDefault()
            axios.post('/signup')
            .then((response) => {
                console.log(response)
            })
            .catch((err) => {
                console.log(err)
            })
            // this.$router.push('/home')
        },
        validateEmail() {
            const emailRegex = /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/
            if (emailRegex.test(this.email.toString()) === false) {
                this.emailErr = true
                this.invalidEmail = true
            } else if (emailRegex.test(this.email.toString()) === true) {
                this.emailErr = false
                this.invalidEmail = false
            }

            this.validateForm()
        },
        validateUsername() {
            switch(true) {
                case this.username.length < 4:
                    this.invalidUsernameErr = "Usernames must be at least 4 characters long."
                    this.invalidUsername = true
                    break
                case this.username.length > 16:
                    this.invalidUsernameErr = "Usernames must be no more than 16 characters long."
                    this.invalidUsername = true
                    break
                default:
                    this.invalidUsername = false
            }
            
            this.validateForm()
        },
        validatePassword() {
            const length = /^(?=.{6,16}$).*/
            const lower = /^(?=.*[[a-z]).*/
            const upper = /^(?=.*[[A-Z]).*/
            const number = /^(?=.*[[0-9]).*/
            const special = /^(?=.*[[!@#$%^&*-]).*/
            
            if (length.test(this.password.toString()) === false) {
                this.passLengthErr = true
                this.invalidPassword = true
            } else { 
                this.passLengthErr = false
            }

            if (lower.test(this.password.toString()) === false) {
                this.passLowerErr = true
                this.invalidPassword = true
            } else { 
                this.passLowerErr = false
            }
            
            if (upper.test(this.password.toString()) === false) {
                this.passUpperErr = true
                this.invalidPassword = true
            } else { 
                this.passUpperErr = false
            }

            if (number.test(this.password.toString()) === false) {
                this.passNumberErr = true
                this.invalidPassword = true
            } else { 
                this.passNumberErr = false
            }

            if (special.test(this.password.toString()) === false) {
                this.passSpecialErr = true
                this.invalidPassword = true
            } else { 
                this.passSpecialErr = false
            }

            if (this.passLengthErr === false && this.passLowerErr === false && this.passUpperErr ===false && this.passNumberErr === false && this.passSpecialErr === false) {
                this.invalidPassword = false
            }

            this.validatePassword2()
            this.validateForm()
        },
        validatePassword2() {
            if (this.password != this.password2) {
                this.invalidPassword2 = true
            } else if (this.password === this.password2) {
                this.invalidPassword2 = false
            }

            this.validateForm()
        },

        validateForm() {
            if (this.invalidEmail === true || this.invalidUsername === true || this.invalidPassword === true || this.invalidPassword2 === true) {
                this.invalidForm = true
            }
            else if (this.invalidEmail === false && this.invalidUsername === false && this.invalidPassword === false && this.invalidPassword2 === false) {
                this.invalidForm = false
            }
        }
    }
}
</script>