<!-- 
    This reset password component sends an email to a user to reset his password.
    Author: Lisa
-->
<template>
   <div class="sufee-login d-flex align-content-center flex-wrap">
        <div class="container">
            <div class="login-content">
                <div class="login-logo">
                    <img class="align-content" src="../images/Logo3.png" alt="">
                </div>
                <div class="reset-form">
                    <div class="form-group">
                        <label id="EText">Enter your Email Address to reset your Password</label>
                        <div class="input-group mb-3">
                            <div class="input-group-prepend">
                                <span class="input-group-text"><i class="fa fa-envelope"></i></span>
                            </div>
                            <input v-model="email" type="email" class="form-control" placeholder="Please enter your Email Address">
                        </div>
                    </div>
                    <button type="button" class="btn btn-success btn-flat m-b-30 m-t-30" @click="resetPassword()">Submit</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return {
                email: '',
            }
        },
        methods: {

            /**
             * Checks if the email address is valid and sends it to the backend.
             * 
             * @author Lisa
             */
            resetPassword: function () {
                function validateEmail(email) {
                var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
                return re.test(String(email).toLowerCase());
                }
                let email = this.email
                if (email === ""){
                    toastr.error("Enter an Email Address!", "Warning", {"timeOut": "1500"})
                } else if (!validateEmail(email)) {
                    toastr.error("The Email is incorrect!", "Warning", {"timeOut": "1500"})
                } else {
                    let email = this.email;
                    this.$store.dispatch('reset_password', {email: this.email}).then(res => {
                        this.$toastr.success('Email to reset your Password was sent!', 'Success', {"timeOut": "1500"})
                        router.push({
                            path: '/resetPasswordInput',
                         });
                        }, err => {
                            this.toastr.error(err, "Warning", {"timeOut": "1500"})
                        })
                }
            }
        }  
    }
</script>
<style scoped>
.reset-form button {
    background-color: #007bff;
    border-color: #007bff;
    width: 100%;
}
.reset-form button:hover {
    background: #0043ff;
    border-color: #0043ff;
}
.reset-form button:active {
    background-color: #007bff !important;
    border-color: #007bff !important;
}
.reset-form {
    background: #ffffff;
    padding: 30px 30px 25px;
    border-radius: 2px;
    box-shadow: 0px 5px 10px 5px #A9A9A9;
}
#EText {
    text-transform: uppercase;
}
.reset-form label {
    color: #878787;
}
</style>