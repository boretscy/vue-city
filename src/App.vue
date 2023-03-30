<template>
    <div class="city-row py-3 px-3 ps-lg-0">
        <div class="city-col text-lg-end">
            <a href="#" class="text-decoration-none text-minus-minus w-100" @click.prevent="toggleList">
                {{ title }}
                <svg xmlns="http://www.w3.org/2000/svg" v-if="showList"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#corner-up"></use></svg>
                <svg xmlns="http://www.w3.org/2000/svg" v-else><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#corner-down"></use></svg>
            </a>
            <div class="city-question b-yagray b-radius-small text-center p-4" v-if="!sel && showQuestion">
                <p>Ваш город {{ items[0].name }}?</p>
                <a href="#" class="c-yawhite c-h-yawhite py-2 px-4 b-radius-small bg-yablue bg-h-yadarkblue text-decoration-none text-minus-minus text-uppercase me-2" @click.prevent="toggleItem(items[0].name, false)">Да</a>
                <a href="#" class="c-yawhite c-h-yawhite py-2 px-4 b-radius-small bg-yayellow bg-h-yadarkyellow text-decoration-none text-minus-minus text-uppercase me-2" @click.prevent="showList = !showList">Нет</a>
            </div>
            <div class="city-list b-radius-small b-yagray bg-yawhite text-start overflow-hidden" v-if="showList" v-click-outside="close">
                <a href="#" role="select-city" class="c-yablack c-h-yablackgray py-2 px-3 text-decoration-none d-block select-city-item-unselected position-relative" @click.prevent="setAll">
                    Все города
                    <span class="select-city-item-check">
                        <svg xmlns="http://www.w3.org/2000/svg"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#check"></use></svg>
                    </span>
                </a>
                <hr class="m-0" />
                <a 
                    v-for="(item, indx) in items"
                    :key="indx"
                    href="#" 
                    class="c-yablack c-h-yablackgray py-2 px-3 text-decoration-none d-block position-relative"
                    :class="{'select-city-item-selected': selected.indexOf(item.name)>=0, 'select-city-item-unselected': selected.indexOf(item.name)<0}"
                    @click.prevent="toggleItem(item.name)"
                    >
                    {{ item.name }}
                    <span class="select-city-item-check">
                        <svg xmlns="http://www.w3.org/2000/svg"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#check"></use></svg>
                    </span>
                </a>
            </div>
        </div>
        <div class="phone-col text-end">
            <a :href="'tel:+'+FormatPhoneIn(phone)" class="text-decoration-none text-uppercase text-minus desktop">{{ FormatPhoneOut(phone) }}</a>
            <a :href="'/cars/'+link+'/favorites'" class="text-decoration-none ms-1" role="topmmenufavorites">
                <svg xmlns="http://www.w3.org/2000/svg"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#favorites"></use></svg>
                <span class="c-yawhite bg-yablue b-white d-flex justify-content-evenly top-menu-icon-label" v-if="favorites.length > 0">{{favorites.length}}</span>
            </a>
            <a :href="'/cars/'+link+'/compare'" class="text-decoration-none ms-1" role="topmmenucompare">
                <svg xmlns="http://www.w3.org/2000/svg"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#compare"></use></svg>
                <span class="c-yawhite bg-yablue b-white d-flex justify-content-evenly top-menu-icon-label" v-if="compare.length > 0">{{compare.length}}</span>
            </a>
        </div>
    </div>
</template>

<script>

export default {
    name: 'App',
    data() {
        return {
            raw: document.getElementById('YAppsCity').getAttribute('items') || null,
            sel: localStorage.getItem('YAPP_SELECTED_CITY') || null,
            showQuestion: false,
            showList: false,
            externalShowList: Boolean(localStorage.getItem('YAPP_SELECTED_CITY_SHOW_LIST')) || false,

            com: localStorage.getItem('CIS_COMPARE') || null,
            fav: localStorage.getItem('CIS_FAVORITES') || null,

            phone: document.getElementById('YAppsCity').getAttribute('phone') || '78612127200'
        }
    },
    computed: {
        items: function() {
            return JSON.parse(this.raw) || []
        },
        selected: function() {
            return (this.sel) ? this.sel.split(',') : []
        },
        compare: function() {
            return JSON.parse(this.com) || []
        },
        favorites: function() {
            return JSON.parse(this.fav) || []
        },
        title: function() {
            let res = 'Все города'
            switch ( this.selected.length ) {
                case 0:
                    res = 'Все города'
                    break
                case 1:
                    res = this.selected[0]
                    break
                case this.items.length:
                    res = 'Все города'
                    break
                default:
                    res = this.selected.length+' города'
                    break
            }
            return res
        },
        link: function() {
            let res = 'new'
            if ( window.location.host == 'yug-avto-expert.ru') res = 'used'

            return res
        }
    },
    directives: {
        clickOutside: {
            bind(el, binding) {
                el.addEventListener('click', e => e.stopPropagation());
                document.body.addEventListener('click', binding.value);
                console.log('sdlgjsdhl')
            },
            unbind(el, binding) {
                document.body.removeEventListener('click', binding.value);
            }
        }
    },
    mounted: function() {
        if ( !this.sel ) {
            this.items.forEach( (e) => {
                this.toggleItem(e.name)
            })
        }
        setInterval(() => {
            if ( localStorage.getItem('YAPP_SELECTED_CITY') != this.sel ) this.sel = localStorage.getItem('YAPP_SELECTED_CITY')
            if ( localStorage.getItem('YAPP_SELECTED_CITY_SHOW_LIST') != this.externalShowList ) this.externalShowList = Boolean(localStorage.getItem('YAPP_SELECTED_CITY_SHOW_LIST'))
            if ( this.externalShowList ) this.showList = true
            if ( localStorage.getItem('CIS_COMPARE') != this.com ) this.com = localStorage.getItem('CIS_COMPARE')
            if ( localStorage.getItem('CIS_FAVORITES') != this.fav ) this.fav = localStorage.getItem('CIS_FAVORITES')
            if ( typeof window.calltouch_phone != 'undefined' && window.calltouch_phone != this.phone ) this.phone = window.calltouch_phone
        }, 100);
    },
    methods: {
        toggleList() {
            this.showList = !this.showList
            localStorage.setItem('YAPP_SELECTED_CITY_SHOW_LIST', '')
        },
        toggleItem(name, flag = true) {
            let indx = this.selected.indexOf(name)
            if ( indx >= 0 ) {
                this.selected.splice(indx, 1)
            } else {
                this.selected.push(name)
            }
            localStorage.setItem('YAPP_SELECTED_CITY', ((this.selected.length)?this.selected.join(','):''))
            this.showQuestion = false
            if ( flag ) this.toggleList()
        },
        setAll() {
            let s = []
            this.items.forEach( (i) => {
                s.push(i.name)
            })
            localStorage.setItem('YAPP_SELECTED_CITY', ((s.length)?s.join(','):''))
            this.showQuestion = false
            this.toggleList()
        },

        close() {
        },
        FormatPhoneOut(q) {
            q = this.FormatPhoneIn(q);
            return '+' + '7' + ' ' + q[1] + q[2] + q[3] + ' ' + q[4] + q[5] + q[6] + '-' + q[7] + q[8] + '-' + q[9] + q[10];
        },
        FormatPhoneIn(q) {
            q = String(q).replace(/[^\d;]/g, '')
            if ( q.length == 10 ) q = '7'+q
            if ( q.length == 7 ) q = '7861'+q
            return '7'+q.slice(1);
        }
    }
}
</script>

<style scoped>

.city-row {
    display: grid;
    justify-content: space-between;
    align-items: center;
    grid-template-columns: 47% 53%;
}
.city-question {
	position: absolute;
    width: calc(100% - 30px);
	top: 60px;
    z-index: 100;
    background-color: var(--yawhite);
}
.city-list {
	position: absolute;
    width: calc(100% - 30px);
	top: 60px;
    z-index: 100;
	font-size: 1rem;
    background-color: var(--yawhite);
}
.city-list a.select-city-item-unselected::after {
	content: 'Выбрать';
	position: absolute;
    right: 0;
    font-size: .85rem;
	color: var(--yamiddlegray);
	display: none;
    background-color: var(--yalightgray);
    padding: 3px 10px;
}
.city-list a.select-city-item-selected::after {
	content: 'Отменить выбор';
	position: absolute;
    right: 0;
    font-size: .85rem;
	color: var(--yamiddlegray);
	display: none;
    background-color: var(--yalightgray);
    padding: 3px 10px;
}
.select-city-item-check {
    position: absolute;
    right: 15px;
    width: 16px;
    height: 16px;
	display: none;
}
.select-city-item-selected .select-city-item-check {
	display: inline-block;
}
.city-list a:hover {
	background-color: var(--yalightgray);
}
.city-list a:hover::after {
	display: inline-block;
}
.city-list svg {
    margin-right: 10px;
	fill: var(--yamiddlegray);
}

@media (max-width: 991px) {
	.city-row {
        background-color: var(--yalightgray);
    }
}
</style>
