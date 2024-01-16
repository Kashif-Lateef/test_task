<template>
    <div id="app">
      <header>
        <button @click="toggleSidebar">&#9776;</button>
        <button v-if="!loggedIn" @click="login">Login</button>
        <span v-if="loggedIn">{{ username }}</span>
      </header>
  
      <main>
        <div class="sidebar" v-show="showSidebar">
          <!-- Sidebar content -->
          <!-- I made only single component for it because it is very small task i can make 3 component for sidebar header and main content  -->
        </div>
  
        <div class="content">
          <div>
            <h2>BTC Price</h2>
            <p>USD: {{ USDPrices.rate }}</p>
            <p>GBP: {{ GBPPrices.rate }}</p>
            <p>EUR: {{ EURPrices.rate }}</p>
          </div>
  
          <div class="image-grid">
            <img v-for="image in images" :src="image.url" :key="image.id" alt="Random Image" class="grid-image" />
          </div>
        </div>
      </main>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        showSidebar: true,
        loggedIn: false,
        username: '',
        USDPrices: {},
        EURPrices: {},
        GBPPrices: {},
        images: [],
      };
    },
    methods: {
      toggleSidebar() {
        this.showSidebar = !this.showSidebar;
      },
      login() {
        // Call randomuser.me/api to get user data
        fetch('https://randomuser.me/api/')
          .then(response => response.json())
          .then(data => {
            const user = data.results[0];
            this.loggedIn = true;
            this.username = `${user.name.first} ${user.name.last}`;
          });
      },
      fetchBTCPrice() {
        // Call api.coindesk.com to get BTC prices
        fetch('https://api.coindesk.com/v1/bpi/currentprice.json')
          .then(response => response.json())
          .then(data => {
            // console.log(data.bpi.EUR.rate);
            this.USDPrices = data.bpi.USD;
            this.GBPPrices = data.bpi.GBP;
            this.EURPrices = data.bpi.EUR;
            
            
          });
      },
      fetchRandomImages() {
        // Call picsum.photos to get random images
        fetch('https://picsum.photos/v2/list?page=1&limit=9')
          .then(response => response.json())
          .then(data => {
            this.images = data.map(item => ({
              id: item.id,
              url: item.download_url,
            }));
          });
      },
    },
    mounted() {
      this.fetchBTCPrice();
      this.fetchRandomImages();
    },
  };
  </script>
  
  <style>
    body {
      font-family: 'Arial', sans-serif;
    }
  
    header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 10px;
      background-color: #333;
      color: white;
    }
  
    main {
      display: flex;
    }
  
    .sidebar {
      width: 300px;
      background-color: #ddd;
      padding: 10px;
      display: flex;
      flex-direction: column;
    }
  
    .content {
      flex-grow: 1;
      padding: 10px;
    }
  
    .image-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
  }

  .grid-image {
    width: 100%;
    height: auto; 
    max-width: 100%;
    border-radius: 8px;
  }

  @media (max-width: 768px) {
    .image-grid {
      grid-template-columns: repeat(2, 1fr);
    }
  }

  @media (max-width: 480px) {
    .image-grid {
      grid-template-columns: 1fr;
    }
  }
  </style>
  