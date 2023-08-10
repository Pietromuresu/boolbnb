
<script>
import axios from 'axios'
import { store } from '../store/store'
import Loader from '../components/partials/Loader.vue';

export default {
  name: "CheckoutResponse",
  components:{
    Loader
  },

  data(){
    return{
      store,

      last_sponsorship: null,
      isLoading: true,
      message: "Operazione riuscita con successo",
      apartment: null,
      sponsorship: null,
      checkoutResponse: "Success",
      countDown: 20
    }
  },

  methods: {
    startCountDown(){
      setInterval(()=>{
        this.countDown--

        if(this.countDown == 0){
          this.$router.push({name: 'home'})
        }
      }, 1000)
    },

    getSponsorship(){
      this.isLoading = true

      axios.get('/sanctum/csrf-cookie')
            .then(() => {
              axios.get('http://127.0.0.1:8000/admin/sponsorships/' + store.sponsorshipId, {
                id: store.sponsorshipId
              } )
              .then(response => {
                this.sponsorship = response.data
                console.log(store.sponsorshipId);
                this.isLoading = false
              })
            })
    },

    getApartment(){
      this.isLoading = true

      axios.get('/sanctum/csrf-cookie')
            .then(() => {
              axios.get(store.adminUrl + 'apartments/' + store.apartmentId, {
                id: store.apartmentId
              } )
              .then(response => {
                this.apartment = response.data
                console.log(store.apartment);
                this.isLoading = false
                this.getLastActiveSponsorship()
              })
            })
    },

    getLastActiveSponsorship() {
      axios.get('sanctum/csrf-cookie')
        .then(() => {
          axios.get(store.adminUrl + 'last-sponsorship/' + this.apartment.slug)
            .then(result => {
              this.last_sponsorship = result.data;
            })
        })
    },

    formatDate (date){
      const formattedDate = new Date(date).toLocaleDateString('it-IT', { weekday:"long", year:"numeric", month:"short", day:"numeric", hour:'2-digit', minute:'2-digit'}) ;
      if(formattedDate === 'Invalid Date'){
        return date
      }else {
        return  formattedDate
      }
    }

  },

  mounted(){
    this.getSponsorship()
    this.getApartment()
    this.startCountDown()

  }
}
</script>

<template>



  <div class="t4-container ">
    <div class="response-container">
      <div v-if="isLoading" class="text-center">
        <Loader />
      </div>
      <div v-else>

        <div class="response-message">
          <h2 class="mb-5">{{ message }}</h2>
          <div class="greeting mb-5">
            <div v-if="checkoutResponse == 'Success'" class="text-success" >
              <p>
                Complimenti, per le prossime <strong> {{ sponsorship.duration }} </strong> ore <strong> {{ apartment.title}} </strong> sarà in primo piano
              </p>

              <div>
                <div class="row justify-content-between" >
                  <div class="col-4  img-house">
                    <img :src="apartment.img_path" alt="">
                  </div>
                  <div class="col-7 pt-4 purchase-deatils" >
                    <div class="text-black">
                      <p class="pm-title">
                        Nome:
                      </p>
                      <p class="pm-text">
                        {{ apartment.title }}
                      </p>
                    </div>
                    <div class="text-black">
                      <p class="pm-title">
                        Fine sponsorizzazione:
                      </p>
                      <p class="pm-text">
                        {{ this.formatDate(last_sponsorship)}}
                      </p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div v-else class="text-danger">
              <p>
                Mi spiace, qualcosa non ha funzionato. Riprova o contatta l'assistenza.
              </p>
            </div>
          </div>


        </div>
      </div>
    </div>
  </div>
  <div class="redirect text-center mt-4">
    <p>
      Sarai reindirizzato alla home tra {{ countDown }} secondi.
    </p>
    <p >
      Altrimenti <router-link :to="{name: 'home'}" ><span class="text-primary"> Torna alla Home </span></router-link>
    </p>
  </div>

</template>

<style lang="scss" scoped>
@use "../../scss/partials/variables" as *;

.t4-container{
  height: calc(100vh - 200px);
  display: flex;
  justify-content: center;
  align-items: center;

  .response-container{
    height: 50%;
    width: 50%;
    min-height:450px;
    margin: 0 auto;
    margin-top: 50px;
    padding: 50px 120px;
    border-radius: 7px;
    box-shadow: 0 0 15px rgb(185, 182, 182);
    color: $dark-gray;

    .greeting{
      p{
        font-size: larger;
      }
    }

    .row{
      width: 100%;
      margin: 0 auto;
      margin-top: 50px;
      height: 150px;
      max-height: 150px;
      box-shadow: 0 0 10px rgb(213, 212, 212);

      .col-4{
        padding: 10px;
        overflow: hidden;
          img{
            height: 125px;
          }

      }


        .purchase-deatils{
          font-size: smaller;
          .pm-title{
            font-weight: 700;
            font-size: 16px;
            color: $dark_gray;

          }
          .pm-text{
            margin-top: 5px;
            margin-bottom: 5px;
          font-size: 14px;
          }
        }


    }
  }
}

</style>
