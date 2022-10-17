<template>
  <a-modal :visible="toggleModal" footer>
    <div class="steps-content">
      <a-card>
        <a-form-model
          style="width: 400px"
          ref="form"
          :model="form"
          :rules="rules"
        >
          <a-form-model-item prop="lang">
            <!-- <a-select v-model="form.lang" @change="langChanged">
              <a-select-option value="en">English</a-select-option>
            </a-select> -->
          </a-form-model-item>
          <div>
            <a-form-model-item label="New Password" prop="password">
              <a-input-password v-model="form.password" />
            </a-form-model-item>
            <a-form-model-item label="Confirmation" prop="confirm">
              <a-input-password v-model="form.confirm" />
            </a-form-model-item>
          </div>
        </a-form-model>
      </a-card>
      <a-button type="primary" @click="submit">Submit</a-button>
    </div>
  </a-modal>
</template>

<script>
export default {
  data () {
    // checks if input has 8 letters/has upper/lower case and includes number
    const validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('Please enter your password'))
        // console.log(value)
      } else if (value.length <= 8) {
        callback(new Error('Password should be atleast 8 characters long.'))
      } else {
        if (this.form.confirm !== '') {
          this.$refs.form.validateField('confirm')
        }
        callback()
      }
    }

    const validatorConfirm = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('Please enter your password again'))
      } else if (value !== this.form.password) {
        callback(new Error('Inconsistent input password twice!'))
      } else {
        callback()
      }
    }
    return {
      form: {
        lang: 'en',
        password: '',
        confirm: ''
      },
      toggleModal: true,
      rules: {
        password: [{ validator: validatePass }],
        confirm: [{ validator: validatorConfirm }]
      }
    }
  },
  methods: {
    handleModal () {
      this.toggleModal = !this.toggleModal
    },
    submit () {
      this.$refs.form.validate(valid => {
        // if invalid - stops execution
        if (!valid) return
        console.log(this.form.password)
        this.$rpc
          .call('ui', 'first_set', {
            lang: 'en',
            username: 'admin',
            password: this.form.password
          })
          .then(() => {
            this.handleModal()
          })
      })
    }
  }
}
</script>
