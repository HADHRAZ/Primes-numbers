# Primes-numbers
Demonstrate of serials adds primes numbers of a number
<?php

$nombre = intval( $_GET['nb'] );

$elements = intval( $_GET['serie'] );  /* Détermine le même nombre de nombres premiers pour le nombre à étudier. Ici : nb */  

$nombreBase = $nombre;

$solution_finale = [];

$nb_solutions = [];

$total_solutions = 0;


/* La méthode de résolution est d'aller du nombre "nb" jusqu'à 1. 
 * Et bizarrement, si ce nombre décrémenté est premier, il est automatiquement associable aux nombres premiers 
 * qui font la somme du nombre "nb", tant la somme de ce nombre aux autres nombres premiers, ne dépasse le nombre "nb".
 */

/* Vu l'importance du nombre de traitement effectué pour un nombre "nb", cet algorithme est donc limité
 * par la puissance du processeur utilisé 
 */


while ( $nombreBase > 1 )
{
    while (verifPremier($nombreBase) === false && $nombreBase >= 1)
    {
        $nombreBase--;
    }
    
    /* Appel de la fonction solution qui détermine tous les résultats du nombre "nb" 
     * selon le nombre d'éléments voulu par résultat.
     * Exemple "serie" ou "$element" = 3, alors il y aura zéro, une, ou plusieurs solutions
     * de nombres premiers qui déterminent chacune la somme du nombre "nb"
     */
     
    $sol = solution( $nombreBase , $nombre , $elements );  
    
    if ( $sol === NULL )   /* Condition sans solutions  */
    {
        $nombreBase--;
        
        continue;
    }
    
    if ( is_array( $sol ) )   /* La solution est un tableau, en l'occurence  :  [ $res , $nb_solution ]  */  
    { 
        array_push( $solution_finale , $sol[ 0 ] ); 
        
        array_push( $nb_solutions, $sol[ 1 ] );
        
        $total_solutions += 1;
    }
    else        /*  La solution est une valeur  :   $temporaire_nb_solution_estimation;   */  
    {
        array_push( $nb_solutions , $sol );
        
        $total_solutions += 1;
    }

    $nombreBase--;
}



/*------------------------------*/
/*    AFFICHAGE DES RESULATS    */
/*------------------------------*/

$tab = $solution_finale;

$n = sizeof($tab);

if ($elements < 3) 
{
    $fact = 5;
} 
else
{
    $fact = 3;
}

$mod = intval( 80 / ( $elements * $fact ) );   /* Détermine le nombre de solution pat ligne de 80 caractères */

if ($mod === 0) 
{
    $mod = 1;
}


for ( $i = 0; $i < $n; $i++ )
{
    for ( $j = 0;  $j < $elements - 1; $j++)
    {
        echo $tab[ $i ] [ $j ] . '  +  ';
    }
    
    echo $tab[ $i ][ $j ] . '&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;';
    
    if ( ( $i + 1 ) % $mod === 0 )
    {
        echo '<br><br>';
    }
}
    
echo '<br><br><br>';

echo 'NOMBRE CHOISI : ' . $nombre; 

echo '<br><br><br>';

echo 'SERIE DE NOMBRES :   ' . $elements; 

echo '<br><br><br>';

echo 'NOMBRE TOTAL DE SOLUTIONS POSSIBLES : ' . $total_solutions;

echo '<br><br>';

sort( $nb_solutions );


$i = 0;

$k = 0;

$res_nb_solutions = [];

while ( $i < sizeof( $nb_solutions ) )
{
    $val = $nb_solutions[ $i ];
    
    $res_nb_solutions[ $k ] = [];
    
    $res_nb_solutions[ $k ][ 0 ] = $val;
    
    $res_nb_solutions[ $k ][ 1 ] = 0;
      
    while ( $val === $nb_solutions[ $i ] &&  $i < sizeof( $nb_solutions ) )
    {
        $res_nb_solutions[ $k ][ 1 ]++;
        
        $i++;
    }
    
    if ( $res_nb_solutions[ $k ] [ 0 ]  ===  $elements )    
    {    
        echo '<span style="color:#C00;">';  
    }
    else
    {
        echo '<span style="color:black;">';
    }
    
    echo 'Série de ' . $res_nb_solutions[ $k ][ 0 ] . ' ==  ' . $res_nb_solutions[ $k ] [ 1 ] . ' solutions';
    
    echo "</span>";
    
    $k++;
    
    if ( $k % 4 !== 0 )
    {
        echo '&nbsp;  &nbsp; &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;  &nbsp;  &nbsp;  &nbsp';
    }
    else
    {
        echo '<br><br><br>';
    }
}
    


/*------------------------------------------*/
/*---- FIN DE l'AFFICHAGE DES RESULATS -----*/
/*-----      FIN DU PROGRAMME      ---------*/
/*------------------------------------------*/


/* La fonction verifPremier permet de verifier si un nombre est premier ou non.
 * Un nombre est premier tant qu'il est divisible par le nombre qui le précède.
 * C'est-à-dire que, pour qu'un nombre soit premier, le dividende de la division du nombre premier 
 * à tous les nombres entiers inférieurs à ce nombre, doivent avoir pour reste de la divison 0.
 * La fonction verifPremier s'arrête lorque le reste de la division de ce nombre par un des nombres
 * qui le précède n'est pas null. 
 */


function verifPremier( $div )
{
    $temporaire = $div - 1;
    
    if ( $temporaire !== 0 )
    {
        while ( $div % $temporaire !== 0 && $temporaire !== 1 )
        {
            $temporaire--;
        }
    }
    
    if ( $temporaire <= 1 ) 
    { 
        return true; 
    } 
    else
    { 
        return false;
    }

}




function solution( $nombrePremier , $nombre , $elements )
{
    $res = [];

    $verif = 0;

    $verif_estimation = 0;

    $temporaire_nb_solution_estimation = 0;

    $temporaire_nb_solution = 0;


    while ( $verif < $nombre && $nombrePremier >= 1 &&  $verif_estimation < $nombre )
    {
        $verifiedPremier = false;

        if ( verifPremier($nombrePremier) == true )
        {
            $plafond = $verif + $nombrePremier;

            $plafond_estimation = $verif_estimation + $nombrePremier;

            $verifiedPremier = true;

        }

        if ( $verifiedPremier == true 
            && $plafond <= $nombre && sizeof( $res ) <  $elements )
        {
            array_push( $res , $nombrePremier);

            $verif += $nombrePremier;

            $temporaire_nb_solution++;

        }

        if ( $verifiedPremier == true  &&  $plafond_estimation <= $nombre )
        {
            $verif_estimation += $nombrePremier;

            $temporaire_nb_solution_estimation++;
        }

        $nombrePremier--;

    }

    /* Solutions de sommes de nombres premiers du nombre décrémenté pour le nombre choisi : "serie" */

    if ( $verif === $nombre && sizeof( $res ) === $elements )   
    {   
         return [ $res , $temporaire_nb_solution ];
    }

    /*  Nombre de solutions de nombres premiers du nombre décrémenté */

    if ( $verif_estimation === $nombre )
    {        
       return $temporaire_nb_solution_estimation;
    }

    return NULL;

}
