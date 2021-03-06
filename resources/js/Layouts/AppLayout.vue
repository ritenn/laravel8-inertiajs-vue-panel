<template>
    <div class="min-h-screen bg-gray-100 flex overflow-y-hidden">

        <nav class="bg-white border-b border-gray-100 fixed top-0 inset-x-0 z-50 h-16 font-medium shadow-lg">
                <!-- Primary Navigation Menu -->
                <div class="mx-auto lg:pr-8">
                    <div class="flex justify-between h-16">
                        <div class="flex">
                            <div class="bg-gray-800 sm:flex px-6 mr-5 text-white ">
                                <button :class="{'sidebar-hidden': hideSidebar, 'sidebar-open': !hideSidebar }" class="sidebar-btn h-full btn focus:outline-none" @click="hideSidebar = !hideSidebar">
                                    <span class="material-icons">toc</span>
                                </button>
                            </div>
                            <!-- Logo -->
                            <div class="flex-shrink-0 flex items-center">
                                <a href="/dashboard">
                                    <jet-application-mark class="block h-9 w-auto" />
                                </a>
                            </div>
                            <!-- Navigation Links -->
                            <div class="hidden space-x-8 sm:-my-px sm:ml-10 sm:flex">
                                <jet-nav-link href="/dashboard" :active="$page.currentRouteName == 'dashboard'">
                                    Dashboard
                                </jet-nav-link>
                            </div>

                        </div>

                        <!-- Settings Dropdown -->
                        <div class="hidden sm:flex sm:items-center sm:ml-6">
                            <div class="ml-3 relative">
                                <jet-dropdown align="right" width="48">
                                    <template #trigger>
                                        <button class="flex text-sm border-2 border-transparent rounded-full focus:outline-none focus:border-gray-300 transition duration-150 ease-in-out">
                                            <img class="h-8 w-8 rounded-full object-cover" :src="$page.user.profile_photo_url" :alt="$page.user.name" />
                                        </button>
                                    </template>

                                    <template #content>
                                        <!-- Account Management -->
                                        <div class="block px-4 py-2 text-xs text-gray-400">
                                            Manage Account
                                        </div>

                                        <jet-dropdown-link href="/user/profile">
                                            Profile
                                        </jet-dropdown-link>

                                        <jet-dropdown-link href="/user/api-tokens" v-if="$page.jetstream.hasApiFeatures">
                                            API Tokens
                                        </jet-dropdown-link>

                                        <div class="border-t border-gray-100"></div>

                                        <!-- Authentication -->
                                        <form @submit.prevent="logout">
                                            <jet-dropdown-link as="button">
                                                Logout
                                            </jet-dropdown-link>
                                        </form>
                                    </template>
                                </jet-dropdown>
                            </div>
                        </div>

                        <!-- Hamburger -->
                        <div class="-mr-2 flex items-center sm:hidden">
                            <button @click="showingNavigationDropdown = ! showingNavigationDropdown" class="inline-flex items-center justify-center p-2 rounded-md text-gray-400 hover:text-gray-500 hover:bg-gray-100 focus:outline-none focus:bg-gray-100 focus:text-gray-500 transition duration-150 ease-in-out">
                                <svg class="h-6 w-6" stroke="currentColor" fill="none" viewBox="0 0 24 24">
                                    <path :class="{'hidden': showingNavigationDropdown, 'inline-flex': ! showingNavigationDropdown }" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                                    <path :class="{'hidden': ! showingNavigationDropdown, 'inline-flex': showingNavigationDropdown }" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                                </svg>
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Responsive Navigation Menu -->
                <div :class="{'block': showingNavigationDropdown, 'hidden': ! showingNavigationDropdown}" class="sm:hidden bg-white z-100 border-solid border-b-4 border-t-2 border-indigo-400">
                    <div class="pt-2 pb-3 space-y-1">
                        <jet-responsive-nav-link href="/dashboard" :active="$page.currentRouteName == 'dashboard'">
                            Dashboard
                        </jet-responsive-nav-link>
                    </div>

                    <!-- Responsive Settings Options -->
                    <div class="pt-4 pb-1 border-t border-gray-200">
                        <div class="flex items-center px-4">
                            <div class="flex-shrink-0">
                                <img class="h-10 w-10 rounded-full" :src="$page.user.profile_photo_url" :alt="$page.user.name" />
                            </div>

                            <div class="ml-3">
                                <div class="font-medium text-base text-gray-800">{{ $page.user.name }}</div>
                                <div class="font-medium text-sm text-gray-500">{{ $page.user.email }}</div>
                            </div>
                        </div>

                        <div class="mt-3 space-y-1">
                            <jet-responsive-nav-link href="/user/profile" :active="$page.currentRouteName == 'profile.show'">
                                Profile
                            </jet-responsive-nav-link>

                            <jet-responsive-nav-link href="/user/api-tokens" :active="$page.currentRouteName == 'api-tokens.index'" v-if="$page.jetstream.hasApiFeatures">
                                API Tokens
                            </jet-responsive-nav-link>

                            <!-- Authentication -->
                            <form method="POST" @submit.prevent="logout">
                                <jet-responsive-nav-link as="button">
                                    Logout
                                </jet-responsive-nav-link>
                            </form>
                        </div>
                    </div>
                </div>
            </nav>
        <!-- Sidebar -->

        <sidebar :hide="hideSidebar"/>

        <!-- Page Content -->
        <main class="flex-1 pt-16 mt-5">
            <!-- Page Heading -->
            <header class="bg-white shadow">
                <div class="flex justify-between max-w-full mx-auto py-6 px-4 sm:px-6 lg:px-8">
                    <div class="inline-flex self-center">
                        <slot name="header"></slot>
                    </div>
                    <button v-if="!isDashboard" class="bg-gray-700 hover:bg-gray-400 font-bold text-white py-2 px-4 rounded inline-flex items-center"
                            @click="redirectBack">
                        <span class="material-icons">arrow_back</span>
                        Return back
                    </button>
                </div>
            </header>
            <slot></slot>
        </main>

        <!-- Modal Portal -->
        <portal-target name="modal" multiple>
        </portal-target>
    </div>
</template>

<script>
    import JetApplicationLogo from './../Jetstream/ApplicationLogo'
    import JetApplicationMark from './../Jetstream/ApplicationMark'
    import JetDropdown from './../Jetstream/Dropdown'
    import JetDropdownLink from './../Jetstream/DropdownLink'
    import JetNavLink from './../Jetstream/NavLink'
    import JetResponsiveNavLink from './../Jetstream/ResponsiveNavLink'
    import Sidebar from "./Sidebar";
    import Button from "../Jetstream/Button";

    export default {
        components: {
            Button,
            JetApplicationLogo,
            JetApplicationMark,
            JetDropdown,
            JetDropdownLink,
            JetNavLink,
            JetResponsiveNavLink,
            Sidebar,
        },

        data() {
            return {
                showingNavigationDropdown: false,
                hideSidebar: false
            }
        },
        methods: {
            logout() {
                axios.post('/logout').then(response => {
                    window.location = '/';
                })
            },
            redirectBack() {
                this.$inertia.visit(this.$page.data.prev);
            }

        },
        computed: {
            path() {
                return window.location.pathname
            },
            isDashboard() {
                return route().current() === 'dashboard';
            }
        }
    }
</script>
<style>
.sidebar-btn .material-icons {
    font-size:48px; display:flex;
}
.sidebar-hidden {
    transform: rotate(0deg);
    transition-delay: 0.5s;
    transition: transform 0.5s;
}
.sidebar-open {
    transform: rotate(180deg);
    transition-delay: 0.5s;
    transition: transform 0.5s;
}
</style>
