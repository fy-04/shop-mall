<template>
<div>
    <div class="card-header">
        <span>{{title}}</span>
        
    </div>
    <div class="card-content">{{count}}</div>
    <div class="card-charts">
        <slot name="charts"></slot>
    </div>
    <div class="card-footer">
        <slot name="footer"></slot>
    </div>
</div>
    
</template>

<script>

export default {
    name:`Detail`,
    props:[
       'title','count'
    ]

}
</script>

<style scoped>
.card-header{
    display: flex;
    justify-content: space-between;
}
.card-content{
    font-size:30px;
    padding: 10px 10px;
}
.card-charts{
    height: 50px;
}
.card-footer{
    border-top: 1px solid black;
    padding-top:10px;
}
</style>

