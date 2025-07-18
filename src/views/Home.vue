<template>
  <div class="home-container">
    <section class="hero-section">
      <div class="hero-content">
        <h1>MCP Registry</h1>
        <p>支持标准化集成，共建智能应用生态，支持动态注册，智能集成的 MCP Registry</p>
      </div>
    </section>
    
    <section class="servers-section">
      <div class="section-header">
        <h2>热门 MCP Servers</h2>
        <span>{{ totalCount }} 个服务器</span>
      </div>
      
      <el-row :gutter="20" v-loading="loading">
        <el-col 
          v-for="server in servers" 
          :key="server.id" 
          :xs="24" 
          :sm="12" 
          :md="8" 
          :lg="6" 
          :xl="6"
        >
          <server-card :server="server" />
        </el-col>
      </el-row>
      
      <div class="pagination-container" v-if="totalCount > 0">
        <el-pagination
          v-model:current-page="currentPage"
          :page-size="pageSize"
          :total="totalCount"
          layout="prev, pager, next"
          @current-change="handlePageChange"
        />
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import { useServersStore } from '../stores/servers'
import ServerCard from '../components/ServerCard.vue'

const store = useServersStore()
const loading = computed(() => store.loading)
const servers = computed(() => store.servers)
const totalCount = computed(() => store.totalCount)

const currentPage = ref(1)
const pageSize = ref(20)

const fetchServers = async (page = 1) => {
  const offset = (page - 1) * pageSize.value
  await store.fetchServers(pageSize.value, offset)
}

const handlePageChange = (page) => {
  currentPage.value = page
  fetchServers(page)
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

onMounted(async () => {
  try {
    await fetchServers()
  } catch (error) {
    console.error('Failed to load servers:', error)
  }
})
</script>

<style lang="scss" scoped>
.home-container {
  width: 100%;
}

.hero-section {
  background: linear-gradient(135deg, var(--primary-color), var(--secondary-color));
  color: white;
  padding: 4rem 2rem;
  text-align: center;
  border-radius: 8px;
  margin-bottom: 2rem;
  
  h1 {
    font-size: 2.5rem;
    margin-bottom: 1rem;
  }
  
  p {
    font-size: 1.2rem;
    max-width: 800px;
    margin: 0 auto;
  }
}

.servers-section {
  .section-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1.5rem;
    
    h2 {
      font-size: 1.8rem;
      font-weight: 600;
    }
    
    span {
      color: #666;
      font-size: 1rem;
    }
  }
}

.pagination-container {
  margin-top: 2rem;
  display: flex;
  justify-content: center;
}
</style>