<template>
  <div class="container mt-4">
    <!-- Website Title -->
    <h1 class="text-center mb-4">Event Management System</h1>
    
    <!-- Image -->
    <div class="text-center mb-5">
      <img src="./assets/event-management.jpg" alt="Event Management" class="img-fluid rounded" style="max-height: 300px;">
    </div>
    
    <!-- Why Choose Us Section -->
    <section class="mb-5">
      <h2 class="text-center mb-4">Why Choose Us?</h2>
      <div class="row">
        <div class="col-12 col-md-6 col-lg-4 mb-4" v-for="(item, index) in features" :key="index">
          <div class="card h-100">
            <div class="card-body">
              <h5 class="card-title">{{ item.title }}</h5>
              <p class="card-text">{{ item.description }}</p>
            </div>
          </div>
        </div>
      </div>
    </section>
    
    <!-- Event Information Table -->
    <section class="mb-5">
      <h2 class="text-center mb-4">Available Events</h2>
      
      <!-- Filter Controls -->
      <div class="row mb-3">
        <div class="col-md-3 mb-2">
          <input type="text" class="form-control" v-model="eventIdFilter" placeholder="Filter by Event ID">
        </div>
        <div class="col-md-3 mb-2">
          <input type="text" class="form-control" v-model="eventNameFilter" placeholder="Filter by Event Name">
        </div>
        <div class="col-md-3 mb-2">
          <input type="number" class="form-control" v-model="durationFilter" placeholder="Filter by Duration">
        </div>
        <div class="col-md-3 mb-2">
          <div class="btn-group" role="group">
            <button type="button" class="btn btn-outline-primary" 
                    v-for="category in categories" 
                    :key="category" 
                    @click="categoryFilter = category"
                    :class="{ 'active': categoryFilter === category }">
              {{ category }}
            </button>
            <button type="button" class="btn btn-outline-primary" 
                    @click="categoryFilter = 'All'"
                    :class="{ 'active': categoryFilter === 'All' }">
              All
            </button>
          </div>
        </div>
      </div>
      
      <!-- Events Table -->
      <div class="table-responsive">
        <table class="table table-striped table-hover">
          <thead class="table-dark">
            <tr>
              <th>Event ID</th>
              <th>Event Name</th>
              <th>Category</th>
              <th>Duration (Hours)</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="event in filteredEvents" :key="event.id">
              <td>{{ event.id }}</td>
              <td>{{ event.name }}</td>
              <td>{{ event.category }}</td>
              <td>{{ event.duration }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>
    
    <!-- Registration Form -->
    <section class="mb-5">
      <h2 class="text-center mb-4">Event Registration</h2>
      <form @submit.prevent="submitForm" class="needs-validation" novalidate>
        <div class="row">
          <div class="col-md-6 mb-3">
            <label for="username" class="form-label">Username</label>
            <input type="text" class="form-control" id="username" v-model="form.username" required>
            <div class="invalid-feedback">
              Please provide a username.
            </div>
          </div>
          
          <div class="col-md-6 mb-3">
            <label for="password" class="form-label">Password</label>
            <input type="password" class="form-control" id="password" v-model="form.password" required>
            <div class="invalid-feedback">
              Please provide a password.
            </div>
          </div>
          
          <div class="col-md-6 mb-3">
            <label for="confirmPassword" class="form-label">Confirm Password</label>
            <input type="password" class="form-control" id="confirmPassword" 
                   v-model="form.confirmPassword" 
                   @input="checkPasswordMatch"
                   required>
            <div class="invalid-feedback">
              Please confirm your password.
            </div>
            <div v-if="passwordMismatch" class="text-danger small">
              Passwords do not match!
            </div>
          </div>
          
          <div class="col-md-6 mb-3">
            <label class="form-label">Event Category</label>
            <div class="form-check" v-for="category in categories" :key="category">
              <input class="form-check-input" type="radio" :id="'category-' + category" 
                     v-model="form.selectedCategory" :value="category">
              <label class="form-check-label" :for="'category-' + category">
                {{ category }}
              </label>
            </div>
          </div>
          
          <div class="col-md-6 mb-3">
            <label for="eventName" class="form-label">Event Name</label>
            <select class="form-select" id="eventName" v-model="form.selectedEvent" required>
              <option value="" disabled>Select an event</option>
              <option v-for="event in filteredEventsByCategory" :key="event.id" :value="event.name">
                {{ event.name }}
              </option>
            </select>
            <div class="invalid-feedback">
              Please select an event.
            </div>
          </div>
        </div>
        
        <button type="submit" class="btn btn-primary" :disabled="passwordMismatch">Register</button>
      </form>
      
      <!-- Registration Summary -->
      <div v-if="submitted" class="mt-4 p-3 bg-light rounded">
        <h4>Registration Summary</h4>
        <p><strong>Username:</strong> {{ form.username }}</p>
        <p><strong>Selected Category:</strong> {{ form.selectedCategory }}</p>
        <p><strong>Selected Event:</strong> {{ form.selectedEvent }}</p>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      features: [
        {
          title: "Wide Variety",
          description: "Choose from hundreds of events across multiple categories."
        },
        {
          title: "Expert Speakers",
          description: "Learn from industry leaders and subject matter experts."
        },
        {
          title: "Networking Opportunities",
          description: "Connect with like-minded professionals and peers."
        },
        {
          title: "Flexible Scheduling",
          description: "Events available at various times to suit your schedule."
        },
        {
          title: "Affordable Pricing",
          description: "Quality events at competitive prices."
        },
        {
          title: "Certification",
          description: "Earn certificates to enhance your professional profile."
        }
      ],
      events: [],
      eventIdFilter: '',
      eventNameFilter: '',
      durationFilter: null,
      categoryFilter: 'All',
      categories: ['Technology', 'Business', 'Marketing', 'Finance'],
      form: {
        username: '',
        password: '',
        confirmPassword: '',
        selectedCategory: 'Business',
        selectedEvent: ''
      },
      passwordMismatch: false,
      submitted: false
    }
  },
  created() {
    this.loadEvents();
  },
  computed: {
   filteredEvents() {
    return this.events.filter(event => {
      // Convert all values to lowercase for case-insensitive matching
      const searchId = this.eventIdFilter.toLowerCase().trim();
      const searchName = this.eventNameFilter.toLowerCase().trim();
      const searchDuration = Number(this.durationFilter);

      // Standardize event data formats
      const eventId = event.id.toString().toLowerCase();
      const eventName = event.name.toLowerCase();
      
      return (
        eventId.includes(searchId) &&
        eventName.includes(searchName) &&
        (this.durationFilter ? event.duration === searchDuration : true) &&
        (this.categoryFilter === 'All' || event.category === this.categoryFilter)
      );
    });
    },
    filteredEventsByCategory() {
      return this.events.filter(event => event.category === this.form.selectedCategory);
    }
  },
  methods: {
    async loadEvents() {
      try {
      const response = await fetch('events.txt');
      const data = await response.text();
      
      // Modified parsing logic
      this.events = data
        .trim()
        .slice(1, -1) // Remove the surrounding []
        .split('},')
        .map(item => {
          const objString = item.trim().replace(/{|}/g, '');
          const entries = objString.split(',')
            .map(entry => {
              const [key, value] = entry.split(':').map(s => s.trim().replace(/'/g, ''));
              return { key, value };
            });
          
          return {
            id: entries.find(e => e.key === 'eventid').value,
            name: entries.find(e => e.key === 'eventname').value,
            category: entries.find(e => e.key === 'category').value,
            duration: parseInt(entries.find(e => e.key === 'durationhour').value)
          };
        });

    }  catch (error) {
        console.error('Error loading events:', error);
        // Fallback data if events.txt can't be loaded
        this.events = [
  {eventid: 'EVT10001', eventname: 'Tech Innovations Conference', category: 'Technology', durationhour: 8},
  {eventid: 'EVT10002', eventname: 'Startup Pitch Day', category: 'Business', durationhour: 6},
  {eventid: 'EVT10003', eventname: 'AI & Machine Learning Summit', category: 'Technology', durationhour: 10},
  {eventid: 'EVT10004', eventname: 'Cybersecurity Workshop', category: 'Technology', durationhour: 4},
  {eventid: 'EVT10005', eventname: 'Digital Marketing Bootcamp', category: 'Marketing', durationhour: 6},
  {eventid: 'EVT10006', eventname: 'Blockchain and Cryptocurrency', category: 'Finance', durationhour: 5},
  {eventid: 'EVT10007', eventname: 'Entrepreneurship Forum', category: 'Business', durationhour: 7},
  {eventid: 'EVT10008', eventname: 'Data Science Hackathon', category: 'Technology', durationhour: 12},
  {eventid: 'EVT10009', eventname: 'Leadership and Management Summit', category: 'Business', durationhour: 9},
  {eventid: 'EVT10010', eventname: 'E-commerce Strategies', category: 'Marketing', durationhour: 6},
  {eventid: 'EVT10011', eventname: 'AI for Business', category: 'Business', durationhour: 8},
  {eventid: 'EVT10012', eventname: 'IoT & Smart Devices Expo', category: 'Technology', durationhour: 7},
  {eventid: 'EVT10013', eventname: 'Brand Strategy and Growth', category: 'Marketing', durationhour: 5},
  {eventid: 'EVT10014', eventname: 'Investment and Wealth Management', category: 'Finance', durationhour: 6},
  {eventid: 'EVT10015', eventname: 'Financial Technology (FinTech) Summit', category: 'Finance', durationhour: 8},
  {eventid: 'EVT10016', eventname: 'AI Ethics and Regulations', category: 'Technology', durationhour: 4},
  {eventid: 'EVT10017', eventname: 'Business Analytics Workshop', category: 'Business', durationhour: 6},
  {eventid: 'EVT10018', eventname: 'SEO and Content Marketing', category: 'Marketing', durationhour: 7},
  {eventid: 'EVT10019', eventname: 'Cryptocurrency Investment Strategies', category: 'Finance', durationhour: 9},
  {eventid: 'EVT10020', eventname: 'Social Media Marketing Trends', category: 'Marketing', durationhour: 5}
];
      }
    },
    checkPasswordMatch() {
      this.passwordMismatch = this.form.password !== this.form.confirmPassword;
    },
    submitForm() {
      this.submitted = true;
      // send to mercury 
    }
  },
  watch: {
    'form.selectedCategory'() {
      // Reset selected event when category changes
      this.form.selectedEvent = '';
    }
  }
}
</script>

<style scoped>
.card {
  transition: transform 0.2s;
}
.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0,0,0,0.1);
}
</style>
