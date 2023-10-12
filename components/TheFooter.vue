<template>
            <div style="width: 90vw; height: 2px; background-color: rgba(0, 0, 0, 0.185); margin-left: 5vw;"></div>
    <div class="footer">

        <div class="left smallFooter">
            <h5> NewsLetter</h5>
            <p>Email</p>

        </div>

        <div class="center smallFooter">
            <h5>Nos réseaux sociaux</h5>
            <p>Nous utilisons nos réseaux pour échanger et apprendre</p>
        </div>

        <div class="right smallFooter" >
            <h5> Liens</h5>
            <div class="liens" style="">
                <div>
                    <p>Contact</p>
                    <p>Dons</p>
                    <p>Nos partenaires</p>
                </div>
                <div style="margin-left: 5%;">
                    <p>Mentions Légales</p>
                    <p>Confidentialité</p>
                    <p>Termes et Conditions</p>
                </div>
            </div>
        </div>
    </div>
</template>

<style>
@media only screen and (min-width: 767px) {
    .footer {
    width: 100vw;
    height: 30vh;

    display: flex;
    justify-content: space-around;
    align-items: center;
    color: black;
    font-weight: bold;
    margin-top: 0%;
    margin-bottom: 2%;
}

p {
    font-weight: 400;
}
.liens {
    display: flex; flex-direction: row; margin-top: 10%;
}
}
@media only screen and (max-width: 767px){
    .footer {
    width: 100vw;
    color: black;
    font-weight: bold;
    margin-bottom: 2%;
}

p {
    font-weight: 400;
}
.smallFooter {
        margin-top: 5%; /* ou une autre valeur appropriée */
    }
    .liens {
    display: flex; flex-direction: row; ;
}
}
</style>