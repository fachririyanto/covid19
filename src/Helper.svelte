<script context="module" lang="ts">
    /**
     * Get day id.
     * 
     * @return {String}
     */
    export function getDayId(): string {
        const date  = new Date();
        return date.getDate() + '' + date.getMonth() + '' + date.getFullYear();
    }

    /**
     * Check if an element has a findable class name.
     * 
     * @reference https://stackoverflow.com/questions/5085567/what-is-the-hasclass-function-with-plain-javascript
     * @param {element} elementObj
     * @param {string} className
     * @return {bool}
     */
    export function hasClass(elementObj: any, className: string) {
        var currentClass = " " + elementObj.className + " ";
        return currentClass.replace(/[\n\t]/g, " ").indexOf(className) > -1;
    }
</script>