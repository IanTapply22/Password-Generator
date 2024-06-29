<template>
  <v-app>
    <v-main class="ml-2">
      <!-- Title and description -->
      <h1 class="text-center">Password Generator</h1>
      <h3>A simplistic and easy-to-use password generator with numerous settings to create the perfect password.</h3>
      <v-col cols="12" md="6">
        <!-- Password preview and copy password button -->
        <v-row>
          <v-text-field width="30%" :model-value="generatedPassword" label="Generated Password" readonly />
          <v-btn height="50" class="ml-4 mt-1" color="green" @click="copyPassword()">Copy Password</v-btn>
        </v-row>
      </v-col>
      <v-col cols="12" md="6">
        <!-- Encryption word header -->
        <h5 class="mb-4 font-weight-light">The length of the password. The encryption word must be null for this to be
          editable.</h5>
        <!-- Password length, only shown when prefix, suffix, or encryption word isn't present -->
        <v-row cols="12" md="6">
          <v-slider v-if="encryptionWord || prefix || suffix" v-model="passwordLength" :min=1 :max="128" label="Password Length" step="1"
            disabled><template v-slot:append>
              <v-text-field v-model="passwordLength" density="compact" style="width: 120px" type="number" hide-details
                single-line></v-text-field>
            </template></v-slider>
          <v-slider v-else v-model="passwordLength" :min=1 :max="128" label="Password Length" step="1"><template
              v-slot:append>
              <v-text-field v-model="passwordLength" density="compact" style="width: 120px" type="number" hide-details
                single-line></v-text-field>
            </template></v-slider>
        </v-row>
        <!-- Settings for what a password can contain -->
        <v-row cols="12" md="6">
          <v-checkbox v-model="includeUppercase" label="Include Uppercase" />
          <v-checkbox v-model="includeNumbers" label="Include Numbers" />
          <v-checkbox v-model="includeSymbols" label="Include Symbols" />
        </v-row>
        <!-- Encryption word field -->
        <h5 class="mb-4 font-weight-light">The encryption word. This overrides the password length.</h5>
        <v-row cols="12" md="6">
          <v-text-field width="30%" v-model="encryptionWord" label="Encryption Word" placeholder="Team 1114" />
        </v-row>
        <!-- Prefix field -->
        <h5 class="mb-4 font-weight-light">What the password is prefixed with. This overrides the password length.</h5>
        <v-row cols="12" md="6">
          <v-text-field width="30%" v-model="prefix" label="Prefix" placeholder="Team 2056" />
        </v-row>
        <!-- Suffix field -->
        <h5 class="mb-4 font-weight-light">What the password is suffixed with. This overrides the password length.</h5>
        <v-row cols="12" md="6">
          <v-text-field width="30%" v-model="suffix" label="Suffix" placeholder="Team 2056" />
        </v-row>
      </v-col>
      <!-- MD5 hash of password -->
      <h3 class="mb-2">MD5 Hash: {{ passwordMD5 }}</h3>
      <!-- Button to generate a new password with the parameters -->
      <v-btn height="50" width="500" color="#b4150b" @click="generatePassword()">Generate Password</v-btn>
    </v-main>
  </v-app>
</template>

<script lang="ts">
import MD5 from "crypto-js/md5";

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      generatedPassword: '',
      passwordLength: 13,
      includeUppercase: true,
      includeNumbers: true,
      includeSymbols: true,
      encryptionWord: '',
      prefix: '',
      suffix: '',
      passwordMD5: ''
    }
  },
  watch: {
    passwordLength() {
      this.generatePassword()
    },
    includeUppercase() {
      this.generatePassword()
    },
    includeNumbers() {
      this.generatePassword()
    },
    includeSymbols() {
      this.generatePassword()
    },
    encryptionWord() {
      this.generatePassword()
    },
    generatedPassword() {
      this.passwordMD5 = MD5(this.generatedPassword).toString();
    },
    prefix() {
      this.generatePassword()
    },
    suffix() {
      this.generatePassword()
    }
  },
  methods: {
    generatePassword() {
      // generate a password based on the selected options
      let characters = 'abcdefghijklmnopqrstuvwxyz'
      let password = ''
      if (this.includeUppercase) characters += 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
      if (this.includeNumbers) characters += '0123456789'
      if (this.includeSymbols) characters += '!@#$%^&*()_+'
      for (let i = 0; i < this.passwordLength; i++) {
        password += characters.charAt(Math.floor(Math.random() * characters.length))
      }
      // encrypt the password with the encryption word
      if (this.encryptionWord) password = btoa(password + this.encryptionWord);
      // add the prefix to the password
      if (this.prefix) password = this.prefix + password;
      // add the suffix to the password
      if (this.suffix) password = password + this.suffix;
      this.generatedPassword = password
    },
    copyPassword() {
      // copy the generated password to the clipboard
      navigator.clipboard.writeText(this.generatedPassword)
      alert('Password copied to clipboard')
    }
  },
  mounted() {
    this.generatePassword()
  }
}
</script>
