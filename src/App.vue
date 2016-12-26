<template>
    <div id="app">
          <indexHead></indexHead>
          <indexContent></indexContent>
          <indexFooter></indexFooter>
    </div>
</template>

<script>
import indexHead from "./components/header"
import indexContent from "components/content"
import indexFooter from "components/footer"

export default {
    components:{
        "indexHead":indexHead,
        "indexContent":indexContent,
        "indexFooter":indexFooter
    }
}
</script>
