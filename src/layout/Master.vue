<template>
    <div class="Master-Layout ">
        <header>
            <nav class="navbar navbar-expand-lg " style="background: #607D8B">
                <div class="container-fluid align-items-baseline">
                    <RouterLink class="navbar-brand text-white" :to="{ name: 'home' }">
                        Hybrid Media Assignment
                    </RouterLink>
                    <button class="navbar-toggler" type="button" data-bs-toggle="collapse"
                        data-bs-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false"
                        aria-label="Toggle navigation">
                        <span class="navbar-toggler-icon"></span>
                    </button>
                    <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
                        <div class="navbar-nav">
                            <RouterLink to="/view"
                                class="d-flex align-items-center bg-transparent px-3 rounded text-white nav-link"
                                aria-current="page">VIEW ALL</RouterLink>
                        </div>
                    </div>
                </div>
            </nav>

        </header>
        <div class="Master-Content">
            <slot></slot>
        </div>
        <!-- <footer>
            <div>
                Copyright HM Assignment 2023
            </div>
        </footer> -->
    </div>
</template>

<script setup lang="ts">
import { RouterLink } from 'vue-router'

</script>

<style lang="scss" scoped>
.Master-Layout {
    // display: flex;
    // flex-direction: column;
    // justify-content: space-between;
    // height: 100vh;


}

footer {
    div {
        background: black;
        padding: 10px 10px;
        text-align: center;
        color: white;
        font-size: .9rem;

    }

    // position: fixed;
    // bottom: 0;
    // width: 100%;
}
</style>