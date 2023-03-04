<template>
  <div id="app">
    <p>Profiles List</p>
    <div class="section">
      <div class="flex-row">
        <label class="label" for="filter">Find profile:</label>
        <input v-model="nameFilter" class="input">
      </div>
      <div class="buttons">
        <button @click="sortAsc">▲</button>
        <button @click="sortDesc">▼</button>
      </div>
      <ProfileCard
        v-for="profile in filteredProfiles"
        :key="profile.id"
        :profile="profile"
        class="profile"
        @commentAdded="(comment) => {profile.comment = comment}"
        @likesIncreasedByOne="() => profile.likes++"
        @likesDecreasedByOne="() => profile.likes--"
        @likesDecreasedByTwo="() => profile.likes = profile.likes - 2"
        @dislikesIncreasedByOne="() => profile.dislikes++"
        @dislikesDecreasedByOne="() => profile.dislikes--"
        @dislikesDecreasedByTwo="() => profile.dislikes = profile.likes - 2"
      />

      <div class="icons-note">
        Icons made by
        <a href="https://www.flaticon.com/authors/freepik" title="Freepik">Freepik</a> from
        <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a>
      </div>
    </div>

    <div class="section">
      <p class="header">Add new profile:</p>
      <div>
        <div class="flex-row">
          <label class="label">Name:</label>
          <input v-model="name" class="input" @focus="nameIsRequired = false" @blur="checkNameBlur">
        </div>
        <p v-if="!nameIsValid && name !== ''" class="medium-txt error-message">The name should include only English characters</p>
        <p v-if="nameIsRequired" class="medium-txt error-message">Name is required</p>
      </div>
      <div>
        <div class="flex-row">
          <label class="label" for="filter">Email:</label>
          <input v-model="email" class="input" @focus="emailIsRequired = false" @blur="checkEmailBlur">
        </div>
        <p v-if="!emailIsValid && email !== ''" class="medium-txt error-message">The email is not in a valid format</p>
        <p v-if="emailIsRequired" class="medium-txt error-message">Email is required</p>
      </div>
      <div>
        <p class="medium-txt" style="color: white">Hold down the Ctrl (windows) or Command (Mac) button to select multiple options.</p>
        <div class="flex-row">
          <div class="flex-row flex-grow-1">
            <label class="label">Specialisation:</label>
            <select v-model="select" multiple>
              <option value="Surgeon">Surgeon</option>
              <option value="Radiologist">Radiologist</option>
              <option value="Cardiologist">Cardiologist</option>
              <option value="Psychiatrist">Psychiatrist</option>
              <option value="Dermatologist">Dermatologist</option>
            </select>
          </div>
          <div class="flex-grow-1">
            <p class="medium-txt" style="background-color: white">{{select}}</p>
          </div>
        </div>
      </div>

        <button @click="checkForm">Add</button>

    </div>
  </div>
</template>

<script>
import ProfileCard from "./components/ProfileCard";

export default {
  name: "App",

  components: {
    ProfileCard
  },

  data() {
    return {
      nameFilter: '',
      name: '',
      email: '',
      errors: [],
      nameIsRequired: false,
      emailIsRequired: false,
      select: [],
      profiles: [
        {
          id: 1,
          name: "Wojciech",
          email: "wojciech@poz.pl",
          description: "Anaesthesiologist",
          likes: 0,
          dislikes: 0
        },
        {
          id: 2,
          name: "Maria",
          email: "maria@poz.pl",
          description: "Radiologist",
          likes: 0,
          dislikes: 0
        },
        {
          id: 3,
          name: "Anna",
          email: "anna@poz.pl",
          description: "Surgeon",
          likes: 0,
          dislikes: 0
        }
      ]
    };
  },

  computed: {
    filteredProfiles() {
      if (this.nameFilter === null) {
        return this.profiles
      }
      return this.profiles.filter((profile) => {
        return (
            profile.name.toLowerCase().includes(this.nameFilter.toLowerCase()) ||
            this.nameFilter === ''
        )
      })
    },
    nameIsValid() {
      let result = true
      let i
      for(i = 0; i < this.name.length; i++) {
        if(!/^[A-Za-z]+$/.test(this.name[i])) {
          result = false
        }
      }
      return result
    },
    emailIsValid() {
      var re = /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/
      return re.test(this.email)
    },
    getSpecialisations() {
      let specialisations = ''
      for(let i = 0; i < this.select.length; i++) {
        if(i === 0) {
          specialisations = specialisations + this.select[i]
        }else {
          specialisations = specialisations + ', ' + this.select[i]
        }
      }
      return specialisations
    },
  },

  methods: {
    sortAsc() {
      this.profiles.sort(function(a, b) {
        return a.likes - b.likes;
      });
    },

    sortDesc() {
      this.profiles.sort(function(a, b) {
        return b.likes - a.likes;
      });
    },
    checkForm(e){
      this.errors = [];

      if (!this.name) {
        this.errors.push("Name is required.");
      }else if(!this.nameIsValid) {
        this.errors.push('The name should include only English characters')
      }
      if (!this.email) {
        this.errors.push('Email is required.');
      } else if (!this.emailIsValid) {
        this.errors.push('The email is not in a valid format');
      }
      if(this.select.length === 0) {
        this.errors.push('You must choose at least 1 Specialisation')
      }

      if (!this.errors.length) {
        this.addProfile()
        return true;
      }else {
        alert(this.errors)
      }
      e.preventDefault();
    },
    addProfile() {
      this.profiles.push({
        id: this.profiles.length + 1,
        name: this.name,
        email: this.email,
        description: this.getSpecialisations,
        likes: 0,
      })
      alert('Doctor ' + this.name + ' added!')
      this.name = ''
      this.email = ''
      this.select = []
    },
    checkNameBlur() {
      if(this.name === '') {
        this.nameIsRequired = true
      }
    },
    checkEmailBlur() {
      if(this.email === '') {
        this.emailIsRequired = true
      }
    },
  }
};
</script>

<style>
#app {
  font-family: "Roboto", helvetica, arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  padding: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  position: relative;

  background: linear-gradient(
    135deg,
    rgba(65, 184, 131, 0.9),
    rgba(52, 73, 94, 0.9)
  );

  font-size: 1.5em;
}

button {
  display: block;
  padding: 1em;
  width: 100%;
  background-color: #41B883;
  border: 1px solid;
  color: #fff;
  cursor: pointer;
  font-size: 0.75em;
  font-weight: 600;
  text-shadow: 0 1px 0 rgba(black, 0.2);
}

.content {
  display: flex;
}

.section {
  width: 100%;
  min-width: 300px;
  padding: 2em;
  margin-top: 30px;
  position: relative;
  background: rgba(0, 0, 0, 0.15);
}

@media screen and (min-width: 600px) {
  .section {
    width: 50vw;
    max-width: 15em;
  }
}

.header {
  color: #fff;
}

.section::before {
  content: "";
  position: absolute;
  top: -2px;
  left: 0;
  height: 2px;
  width: 100%;
  background: #35c3c1;
}

.flex-row {
  display: flex;
  margin-bottom: 1em;
}

.label {
  width: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;

  background: #f5f6f8;
}

.input {
  flex: 1;
  padding: 1em;
  border: 0;
  color: #8f8f8f;
  font-size: 1rem;
}

.buttons {
  display: flex;
  box-shadow: 0 10px 10px rgba(0, 0, 0, 0.25), 0 5px 5px rgba(0, 0, 0, 0.22);
  margin-top: 30px;
}

.profile {
  margin-top: 20px;
}

.icons-note {
  margin-top: 30px;
  font-size: 10px;
}
.medium-txt {
  font-size: 15px;
}
.error-message {
  color: red;
}
</style>
