<template>
  <div v-if="isLoggedIn">
    <nav class="navbar sticky-top flex-md-nowrap p-0" id="navbar-navigation">
      <a class="navbar-brand col-sm-3 col-md-2 mr-0" href="#">Fantasea Admin</a>
      <ul class="navbar-nav px-3">
        <li class="nav-item text-nowrap">

          <a class="nav-link" href="#">{{ email }}</a>
        </li>
      </ul>
    </nav>
    <div class="container-fluid">
      <div class="row">
        <nav class="col-md-2 d-none d-md-block bg-light sidebar">
          <div class="sidebar-sticky">
            <ul class="nav flex-column">
              <li class="nav-item">
                <a class="nav-link active" href="#">
                  <span data-feather="home"></span>
                  Dashboard
                </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#" @click="openUserTab">
                  Users
                </a>
              </li>
              <li class="nav-item">

                <a class="nav-link" href="#" @click="openDestinations">
                  Destinations
                </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#" @click="openInboxTab">
                  Inbox
                </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#" @click="openReportTab">
                  Reports
                </a>
              </li>
            </ul>

            <h6 class="sidebar-heading d-flex justify-content-between align-items-center px-3 mt-4 mb-1 text-muted">
              <span>Otherssss</span>
              <a class="d-flex align-items-center text-muted" href="#">

              </a>
            </h6>
            <ul class="nav flex-column mb-2">
              <li class="nav-item">
                <router-link to="/settings-admin">
                  <a class="nav-link" href="#">
                    Settings
                  </a>
                </router-link>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#" @click="singOut">

                  Log out
                </a>
              </li>
            </ul>
          </div>
        </nav> |
        <main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">




        </main>
      </div>
    </div>

  </div>

  <router-view />
</template>

<script>
/* eslint-disable */
import { getAuth, onAuthStateChanged, signOut } from 'firebase/auth'
import { getDatabase, ref, update, get, child } from "firebase/database";


export default {
  name: 'adminApp',

  data() {
    return {
      username: '',
      email: '',
      isLoggedIn: false,
      userID: '',
    };
  },
  mounted() {
    const auth = getAuth();
    const db = getDatabase();
    const dbRef = ref(getDatabase());
    onAuthStateChanged(getAuth(), (user) => {
      if (user) {
        this.email = user.email;
        this.userID = user.uid;
        get(child(dbRef, '/users/admin/' + this.userID)).then((snapshot) => {
          if (snapshot.val().user_type == 'admin') {
            console.log('Admin account ' + user.email)
            this.$router.push('/');
            this.isLoggedIn = true;       
            this.$router.push('/manage-users');
            update(ref(db, '/users/admin/' + user.uid), {
              status: 'online',
            })
          }
          else {
            alert('Unauthorized access is forbidden');
            this.isLoggedIn = false;
            signOut(auth).then(() => {

            })
            this.$router.push('/login');
          }
        });
      } else {
        signOut(auth).then(() => {

        })
        this.isLoggedIn = false;
        this.$router.push('/login');
      }
    });
  },

  methods: {
    singOut() {
      const db = getDatabase();
      const auth = getAuth();
      update(ref(db, '/users/admin/' + auth.currentUser.uid), {
        status: 'offline',
      })
      signOut(auth).then(() => {
        // Sign-out successful.
      }).catch((error) => {
        // An error happened.
      });
    },
    openUserTab() {
      this.$router.push('/manage-users');
    },
    openReportTab() {
      this.$router.push('/reports');
    },
    openInboxTab() {

      this.$router.push({ name: 'inbox-admin', params: { id: this.email } })
    },
    openDestinations() {
      this.$router.push('/destinations');
    }
  }
}
</script>

<style >
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}

/*
 * Sidebar
 */

.sidebar {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  z-index: 100;
  /* Behind the navbar */
  padding: 0;
  box-shadow: inset -1px 0 0 rgba(0, 0, 0, .1);
}

.sidebar-sticky {
  position: -webkit-sticky;
  position: sticky;
  top: 48px;
  /* Height of navbar */
  height: calc(100vh - 48px);
  padding-top: .5rem;
  overflow-x: hidden;
  overflow-y: auto;
  /* Scrollable contents if viewport is shorter than content. */
}

.sidebar .nav-link {
  font-weight: 500;
  color: #333;
}

.sidebar .nav-link .feather {
  margin-right: 4px;
  color: #999;
}

.sidebar .nav-link.active {
  color: #007bff;
}

.sidebar .nav-link:hover .feather,
.sidebar .nav-link.active .feather {
  color: inherit;
}

.sidebar-heading {
  font-size: .75rem;
  text-transform: uppercase;
}

/*
 * Navbar
 */
.navbar-dark{
  width: 100%;
  color:red;
}

.navbar-brand {
  padding-top: .75rem;
  padding-bottom: .75rem;
  font-size: 1rem;
  background-color: rgba(0, 0, 0, .25);
  box-shadow: inset -1px 0 0 rgba(0, 0, 0, .25);
}

.navbar .form-control {
  padding: .75rem 1rem;
  border-width: 0;
  border-radius: 0;
}

.form-control-dark {
  color: #fff;
  background-color: rgba(255, 255, 255, .1);
  border-color: rgba(255, 255, 255, .1);
}

.form-control-dark:focus {
  border-color: transparent;
  box-shadow: 0 0 0 3px rgba(255, 255, 255, .25);
}
#navbar-navigation{
  width: 100%;
}
</style>
