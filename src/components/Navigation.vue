<template>
  <nav id="nav" class="navbar">

    <ul class="sections">
      <li class='logo'>docker friend</li>
      <li v-bind:class="{active: this.$route.fullPath.includes('local') }" class='local'><router-link to="/local">local</router-link></li>
      <li v-bind:class="{active: this.$route.fullPath.includes('cloud') }" class='cloud'><router-link to="/cloud">cloud</router-link></li>
    </ul>
    <div class="current_profile">
      <DropMenu></DropMenu>

    </div>
  </nav>
</template>

<script>
  import DropMenu from './dropmenu'
  import Nes from 'nes'
  import { mapActions } from 'vuex'
  const client = new Nes.Client(`ws://${process.env.API_HOST}`);

  export default {
    name: 'navigation',
    props: ['router'],

    // methods: {
    //   changeProfileSelection() {
    //     console.log();
    //   }
    // },
    created() {

      client.connect(() => {
        client.subscribe('/aws', (res) => {
          this.$store.dispatch('changeProfileSelection', res)
        }, (err) => {
          console.log(err)
        })
        client.subscribe('/errors', (res) => {
          console.log(res)
          if ( res ) {
            this.$toast.open({
                    duration: 10000,
                    message: `${res.msg}`,
                    position: 'is-top',
                    type: 'is-danger'
            })
          } else {console.log(res)}
        }, (err) => {
          console.log(err)
        })
      })
    },
    components: {DropMenu}
  }
</script>

<style lang='scss'>
  @import "../scss/global.scss";
  .navbar {
    display:flex;
    position:fixed;
    justify-content: space-between;
    background: white;

    top:0;right:0;left:0;
    z-index:10;
    // border-bottom:1px solid rgba(123,123,123,.8);
    box-shadow:1px 1px 1px rgba(0,0,0,.3);
    h2 {
      font-size:16px;
    }

    .sections {
      li {
        padding:20px 20px;
        letter-spacing:1px;
      }
      .logo {
        font-weight: 100;
        padding-left: 24px;
        font-size:24px;
        color:$blue;
        margin-right:40px;
        // font-family: 'Roboto Slab', serif !important;
        // text-transform:uppercase;
      }
      .local, .cloud {
        a {
          color: $blue;
          font-size: 24px;
          padding: 5px 21px 5px 21px;
          transition: color .2s ease-in-out;
          font-weight: 100;
          letter-spacing: 3px;

          &:hover {
            cursor:pointer;
            color:$black;
          }
        }
        &.active {
          background-color:$blue;

          &:hover {
            cursor:pointer;
            color:$dark_blue;
          }
          a {
            color:white;
          }
        }

      }
    }
  }
</style>
