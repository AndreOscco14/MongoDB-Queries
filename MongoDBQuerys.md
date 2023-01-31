Para iniciar MongoDB ---> mongo

Para ver las BBDD ---> show dbs

Para entrar a ver la BBDD ---> use (NombreBBDD) (CREA LA BBDD)
                            use nombreBDD 

Mostrar datos de la BBDD ---> show collections  (Muestra lo que lleva dentro Collecciones).

Para ver los datos dentro de la BBDD ---> db.(NombreColeccion).find()
                                       db.(NombreColeccion).find().pretty() ---> Muestra datos más ordenado

Hacer una consulta para saber un nombre ---> (NombreColleccion).find({name: '' });
                                            User.findOne({ username: 'Andre'});  --> Otra Forma



--> Actualizar y crear un campo Telefonos: { Con otros subCampos Casa y Movil}
db.users.update({_id: ObjectId("63b2c06f1c24b62bbca3278e")},{$set: {telefonos: {casa: "123456789", movil: "987654321" }}})


DeleteOne: solo elimina el primer registro que encuentre

=================================================================================
                            OPERADORES CONDICIONALES 
==================================================================================
 $lt : menor que <
 $lte: Menor igual que <=
 $gt: Mayor que >
 $gte: Mayor igual que >=
 $ne: No igual 

 $set: agregar 
 $unset: Quitar campo

=================================================================================
db.empleados.deleteMany({'$and': [{'ciudad': 'Madrid'}, {'edad': {$gte:'18'}}]})
Elimina el usuario que es de Madrid y su edad es mayor igual que 18
=================================================================================

$set : con update te actualiza unicamente el dato en especifico
> db.empleados.update({'_id':ObjectId("63be86af30411bb8a5246511")},{$set: {edad:40}})
--
{
	"_id" : ObjectId("63be86af30411bb8a5246511"),
	"nombre" : "paquillo",
	"edad" : 40,
	"sexo" : "M",
	"municipio" : "S.I"
}
=====================================================================================
            METER UN NUEVO DATO A LA VEZ QUE ACTUALIZAMOS ('$set')

db.empleados.update({'_id': ObjectId("63be86af30411bb8a5246511")}, {$set: {fechanac: ISODate('2000/05/14')}})

{
	"_id" : ObjectId("63be86af30411bb8a5246511"),
	"nombre" : "paquillo",
	"edad" : 40,
	"sexo" : "M",
	"municipio" : "S.I",
	"fechanac" : ISODate("2000-05-14T00:00:00Z")
}

///////////////////////////////////////////////////////////////////////////////////////////////////////

update: Solo actualiza la primera que encuentra, no actualiza muchos

db.empleados.update({edad: {$gte: 30}}, {$set: {comunidad: 'Valencia'}}) 

--> actualiza para la primera persona mayor que 30 , cambia su comunidad por Valencia
    ("Solo actualiza una persona aunque hayan 5 mayores de 30")

--> Para que actualize a varias personas que cumplan con la condicion, al final se le agregará un {multi:true}
db.empleados.update({edad: {$gte: 30}}, {$set: {comunidad: 'Mallorca'}}, {multi:true})

///////////////////////////////////////////////////////////////////////////////////////////////////////





===================================
* CREAR UN CAMPO EMBEBIDO CON $set

db.empleados.update({'_id': ObjectId("63be86af30411bb8a5246511")},{$set: {deporte: ['natacion','tennis']}}})



=====================================================================================
                     QUITAR UN CAMPO DENTRO DEL DATO ($unset)

db.empleados.update({'_id': ObjectId("63be86af30411bb8a5246511")}, {$unset: {edad:1}})

{
	"_id" : ObjectId("63be86af30411bb8a5246511"),
	"nombre" : "paquillo",
	"sexo" : "M",
	"municipio" : "S.I",
	"fechanac" : ISODate("2000-05-14T00:00:00Z")
}

===============================================================================================
                            CONDICIONALES RANGOS CON $and y $or

 db.empleados.update({$and: [{edad: {$gt:30, lt: 40}}]})

  db.empleados.update({$and: [{edad: {$gt:40, $lt: 55}}]}, {$set: {extra: 5000}},{multi:true})

===============================================================================================
                        CONSULTAS FIND( Orden Ascendiente y Descendiente )

db.empleados.find({}).sort({name: 1})   --> Ordena de forma ascendente
db.empleados.find({}).sort({name: -1})  --> Ordena de forma descendente

 
Mostrar solo ciertos datos:

db.empleados.find({edad: 60}, {nombre:1, edad:1,sexo:1}) 
---> Muestra edad, sexo y nombre de todos los empleados que tengan 60

db.empleados.find({edad: {$gte: 30}} , {nombre:1,edad:1,sexo:1}).sort({edad:1})
---> Muestra edad, sexo y nombre de todos los empleados que tengan más de 60 años y lo muestra en forma ordenada

db.empleados.find({ $and:[{sexo: 'F'}, {'edad': {$gte:20}}]})


===================================================================================================
                                    Query CON $and y $or
db.empleados.find({
                    $and:[
                           {
                              $or: [
                                    {comunidad: 'Madrid'} ,
                                    {comunidad: 'Mallorca' }
                                    ]
                            },
                            {
                                $and: [
                                    {sexo: 'F'},
                                    {edad: {$gte: 60}}
                                       ]
                            }
                           ] 
                         })

--> Encuentra de la collecion empleados persona de Sexo 'F' >= 60 y  de Madrid o Mallorca
--> Encuentra de la collecion empleados persona de Madrid "O" Valencia "Y" de Sexo 'F'  "Y " >= 60

===================================================================================================
                                EXPRESIONES REGULARES E IRREGULARES 
                                
db.empleados.find({nombre: /^J/})
Encuentra personas que el nombre empiece con "J"

db.empleados.find({nombre: /^j/i})
Encuentra personas que empiecen con J, pero con la "i" puede buscar mayusculas y minusculas

db.empleados.find({nombre: /pe$/i})
Encuentra persona que su nombre termine con pe con la "i" puede buscar mayusculas y minusculas

db.empleados.find({nombre: {$in: [/a$/i, /^j/i]}})
Devuelve personas que su nombre termine en "a" y empiece con "j" , aunque si algun nombre cumple con una
de esas condiciones, devuelve igualmente. (Si termina o empieza)

db.empleados.find({nombre: {$regex: /^j.*a/i}})
Devuelve todos los empleados que empicen por "J" y terminen en "a"

db.empleados.find({area: {$regex:/^[S][A|O][P|S]*/i} })

=========================================================================================================

db.empleados.find({area: {$in: ['compras','ventas']}})
Solo devuelve personas que están en compras y ventas

=========================================================================================================
                                                PUSH

db.empleados.update({'_id': ObjectId("63beaf62b54cd23fe40bb0df")},
                     {$push: 
                            {deportes: 
                                        {
                                           'pelota': 'futbol',
                                            'acuaticos': 'natacion',
                                            'animales': 'Danzar con lobos'
                                         }
                             }
                         } 
                    )
=========================================================================================================

$each: permite agregar varios campos de tipo Array , cada registro (RECORRE)

db.empleados.update(
                      {sexo: 'M'},
                      {$push: {produccion: {
                                            $each: [
                                                {'nombre': 'p1', 'piezas':4},
                                                {'nombre': 'p2', 'piezas':1},
                                                {'nombre': 'p3', 'piezas':9},
                                                {'nombre': 'p4', 'piezas':10}
                                             ]
                                            } 
                      } }
                    )
    $push: agrga un array 

=========================================================================================================
zonast= [{nombre: 'centro', salario:1000},
          {nombre: 'sur',salario: 4000}]
            --------------------------
 db.empleados.update({'_id': ObjectId("63beaf62b54cd23fe40bb0de")},
      {$push: {zonastrabajo: { $each: zonast, $slice:2} 
   }}
)
 
Al doc empleados con el ID, se le hace un push, que creee onas de trabajo y que el nuevo campo, obtenga
los valores de "zonast" y que recorrada todos los registros y se quede con solo 2.


=========================================================================================================

 db.empleados.update({'_id': ObjectId("63beaf62b54cd23fe40bb0de")},
                    {$pop: {'produccion': 1 }})

$pop: --> Sirve para eliminar el primero ( 1 ) y si es en negativo se eliminan los ultimos 
( -5 )--> se eliminan los ultimos 5 elementos que entraron


=========================================================================================================

    db.empleados.update({'_id': ObjectId("63beaf62b54cd23fe40bb0de")},
                { $pull: {'produccion': {'piezas': {$gte:9}}}})

$pull --> elimina 



    ========================================================================================================
     |||||||||||||||||||||||||||||||||| AGGREGATE $match $group $sum $avg $multip  ||||||||||||||||||||||||||
     =========================================================================================================

$aggregate: Crea un conjunto de sentencias
--------------------------------------------------
db.getCollection('empleados').find({})

db.empleados.remove({})

db.empleados.insert({nombre: 'Andre', salario: 1000, sexo: 'M', diast: 7, area: 'Ventas', edad: 30, fechapago:ISODate('2023-01-12')})
db.empleados.insert({nombre: 'Paty', salario: 2000, sexo: 'F', diast: 6, area: 'Ventas', edad: 45, fechapago:ISODate('2023-01-01')})
db.empleados.insert({nombre: 'Luis', salario: 3000, sexo: 'M', diast: 6, area: 'Compras', edad: 20, fechapago:ISODate('2023-02-01')})
db.empleados.insert({nombre: 'Rodrigo', salario: 5000, sexo: 'M', diast: 5, area: 'Compras', edad: 25, fechapago:ISODate('2023-02-10')})
db.empleados.insert({nombre: 'Laura', salario: 4000, sexo: 'F', diast: 7, area: 'Produccion', edad: 50, fechapago:ISODate('2023-02-15')})


db.empleados.aggregate(
    [
        { $match: 
            { $or: [ {sexo: 'M'}, {sexo: 'F'} ] }
         },
         {
            $group: {
                _id: '$sexo', total: {$sum: '$salario'}
            }
         }
    ]
)
 
-> Siempre van dentro de corchetes

$match --> en esta instruccion se coloca todas las condiciones que quieres que se cumpla (AGRUPA) (CONSIDERE)

*//* Para llamar a un dato de la BBDD Se coloca entre '$sexo'   ,, '$salario'

$group -->  indicar que campos va agrupar y cuales generará y cuales son las condiciones que tomara en cuenta 
para hacer el agrupamiento ($match)
 


--------------------------------------------------------------------------------------
--- Saber cual es el salario de Mujeres mayores de 20 años de edad según su área ----
--------------------------------------------------------------------------------------
Agrupe por el area y sume todos los salarios, pero ahora solo que considere las mujeres y en edad > 20.


db.empleados.aggregate(
    [
        { $match: 
             {sexo: 'F', edad: {$gte: 20} } 
         },
         {
            $group: {
                _id: '$area', total: {$sum: '$salario'}
            }
         }
    ]
)




-------------------------------------------------------
-- Cuanto es lo que se pago a los empleados en Enero --
-------------------------------------------------------



db.empleados.aggregate(
    [
         {
        $group: {
                _id: {
                    mes: {$month: '$fechapago'},
                    año: {$year: '$fechapago'}
                  },
                   salariomensual: { 
                        $sum: {$multiply: ['$salario','$diast']}
                    },
                   diaspromedio: {$avg: '$diast'}, 
                   numeroempleados: {$sum:1}
            }
        }     
    ]
)

-------------------------------------------------------
-- $project: solo imprime nombre y salario  --
-------------------------------------------------------

db.empleados.aggregate([
         {
            $project: {
                nombre: 1,
                salario: 1
            }
          }
        ])




db.empleados.aggregate([
         {
            $project: {
                nombreempleado: '$nombre',
                salario: 1,
                status: {
                    $cond: if: {
                        $lte: ['$diast', 6]
                    }, then: 'Faltas',
                     else: 'Todo ok'
                }
            }
          }
        ])




db.empleados.aggregate([
         {
            $project: 
               {
                nombreempleado: '$nombre',
                salario: 1,
                status: {
                    $cond:{if: {
                        $lte: ['$diast', 6]
                    }, then: 'Faltas',
                     else: 'Todo ok'
                }
               }
            }
          }
        ])


========================================================================================================

db.empleados.aggregate([
         {
            $project: 
               {
                nombreempleado: '$nombre',
                salario: 1,
                status: {
                    $cond:
                    {if: {
                        $lte: ['$diast', 6]
                        },
                       then: 'Faltas',
                     else: 'Todo ok'
                }
               },
              bono: {$cond: 
                    {if: { $lte: ['$diast',6]},then:{$multiply: ['$diast',10]},else:{ $multiply: ['$diast',50]}
                     }
                 } 
            } 
          },
          {
              $group: {_id: '$status', subtotal: {$sum: '$bono'}}
           },
           {
               $group: {_id: '', total:{$sum: '$subtotal'}}
           }
        ])

========================================================================================================


LINKS MONGO: https://www.youtube.com/watch?v=XinaNVwid9w&list=PLpk46uAL3qUG3NYfYJkmREZGCX5yM3P7u&index=12 
LINK MONGO 1:N M:N --> https://www.youtube.com/watch?v=Nsog0pjG-5U








========================================================================================================
////////////////////////////////////////////////////////////////////////////////////////////////////////
========================================================================================================
========================================================================================================
             aggregate con $lookup $replaceRoot, $project $match y $sort
========================================================================================================

db.grupos.insert({'_id': 1.0, nombreg: 'ITIC81', carrera: 'Ingenieria TIC'})
db.grupos.insert({'_id': 2.0, nombreg: 'ITIC82', carrera: 'Ingenieria TIC'})
db.grupos.insert({'_id': 3.0, nombreg: 'RT 51', carrera: 'Redes'})


db.alumnos.insert({'_id': 1.0, matricula: '2233225566', nombre: 'Paty', app: 'Lopez', sexo: 'F' , idg:1 , edad: 20})
db.alumnos.insert({'_id': 2.0, matricula: '2233225345', nombre: 'Joel', app: 'Mendez', sexo: 'M' , idg:1 , edad: 25})
db.alumnos.insert({'_id': 3.0, matricula: '2233285868', nombre: 'Fernando', app: 'Ramirez', sexo: 'M' , idg:2 , edad: 28})
db.alumnos.insert({'_id': 4.0, matricula: '2233756765', nombre: 'Laura', app: 'Herrera', sexo: 'F' , idg:2 , edad: 25})
db.alumnos.insert({'_id': 5.0, matricula: '2234568596', nombre: 'Memo', app: 'Torres', sexo: 'M' , idg:3 , edad: 30})
db.alumnos.insert({'_id': 6.0, matricula: '2296856777', nombre: 'Karla', app: 'Lopez', sexo: 'F' , idg:3 , edad: 23})

______________________________________________________________________
                Conexion incluyendo clave foránera
______________________________________________________________________

db.alumnos.aggregate([
            {
                $lookup: {
                    from: 'grupos', 
                    localField: 'idg', foreignField: '_id',
                    as: 'grupos'
                }
            }
])


--> Se queda con los alumnos Masculinos
db.alumnos.aggregate([
            {
                $lookup: {
                    from: 'grupos', 
                    localField: 'idg', foreignField: '_id',
                    as: 'grupos'
                }
            },
            { $match: { sexo: 'M'}}
])


$project --> Solo nos permite visualizar los campos que solicitamos
db.alumnos.aggregate([
            {
                $lookup: {
                    from: 'grupos', 
                    localField: 'idg', foreignField: '_id',
                    as: 'grupos'
                }
            },
            { 
               $match: { sexo: 'M', 'grupos.carrera': 'Ingenieria TIC'}
            },
            {
                $project: {
                        _id:0,
                        matricula: 1,
                        nombre: 1,
                        sexo: 1,
                        carrera: '$grupos.carrera'
              }
        }
])

__________________________________________________________________________________
Mete datos de la colleccion Grupos al de Alumnos (Lo pone de Raiz)
db.alumnos.aggregate([
            {
                $lookup: {
                    from: 'grupos', 
                    localField: 'idg', foreignField: '_id',
                    as: 'grupos'
                }
            },
          {
              $replaceRoot: {newRoot: {$mergeObjects: [{$arrayElemAt: ['$grupos', 0]}, "$$ROOT"]}}
          }
])


========================================================================================================
////////////////////////////////////////////////////////////////////////////////////////////////////////
========================================================================================================

sync function filterCodePromotions(req, res){
    try {
        const resp = await querysService.filterCodePromotions(page)
        return response(res, Status.OK, resp)
    } catch (error) {
        return response(res, Status.INTERNAL_SERVER_ERROR)
    }
}
----------------------------------------------------------------------------------------------------------



async function usersMvp(req, res){
    try{
        let from = {
            year: req.query.fyear || null,
            day: req.query.fday || null,
            month: req.query.fmonth || null
        }
        let to = {
            year: req.query.tyear || null,
            day: req.query.tday || null,
            month: req.query.tmonth || null
        }

         let page = (req.query.page || 1) == 0 ? 1 : req.query.page;
       
        const resp = await querysService.usersMvp(from,to, page)

        return response(res, Status.OK, resp)
    } catch (error) {
        logger.error({ruta: 'querys.controller.js => usersMvp()', message: error.message})
        return response(res, Status.INTERNAL_SERVER_ERROR)
    } 
}


async function getChooseMvp(req, res) {
    try {
        let from = {
            year: req.query.fyear || null,
            day: req.query.fday || null,
            month: req.query.fmonth || null
        }

        let to = {
            year: req.query.tyear || null,
            day: req.query.tday || null,
            month: req.query.tmonth || null
        }

        let page = (req.query.page || 1) == 0 ? 1 : req.query.page;

        const resp = await querysService.getChooseMvp(from,to, page)

        return response(res, Status.OK, resp)
    }catch(error) {
        logger.error({ruta: 'querys.controller.js => getChooseMvp()', message: error.message})
        return response(res, Status.INTERNAL_SERVER_ERROR)
    }
}



async function getTotalNominados(req, res){
    try{
        const resp = await querysService.getTotalNominados()
        return response(res, Status.OK, resp)
    } catch (error) {
        logger.error({ruta: 'querys.controller.js => getTotalNominados()', message: error.message})
        return response(res, Status.INTERNAL_SERVER_ERROR)
    } 
}

async function getTotalMvp(req, res){
    try{
        const resp = await querysService.getTotalMvp()
        return response(res, Status.OK,resp)
    }catch(error){
        return response(res, Status.INTERNAL_SERVER_ERROR)
    }
}


module ezports:
    usersMvp,
    getChooseMvp,
    getTotalNominados,
    getTotalMvp


========================================================================================================
////////////////////////////////////////////////////////////////////////////////////////////////////////
========================================================================================================

async function getChooseMvp(from, to){
    let fromDateNominado = moment([from.year, from.month - 1, from.day]);
    let toDateNominado = moment([to.year, to.month - 1, to.day]);

//----------------------------------------------------------------------------------------------------
    // return await matchesModel.find(
    // {
    //       $and: [
    //          { _userMvp: {$ne: null }} ,
    //          { date: { $gte: fromDateNominado}},
    //          { date: { $lte: toDateNominado}},
    //         ]
    // })
    // .populate( {path:'_userMvp', select: 'firstName lastName pictureUrl'})
    // .limit(10000)
    // .exec()
//----------------------------------------------------------------------------------------------------
        // let usersMvp = await usersModel.aggregate([
        //     {
        //         $group: {
        //             _id: "$_matchesMvp",
        //             RESULT: {
        //                 $sum: {
        //                     $cond: [
        //                         {
        //                             $and: [ 
        //                                 // { _userMvp: {$ne: null }},
        //                                 { $gte: [ "$createdAt", new Date(fromDateNominado)]},
        //                                 { $lte: [ "$createdAt", new Date(toDateNominado)]}
        //                             ]
        //                         },
        //                        1,
        //                      0 
        //                     ]
        //                 }
        //             }
        //         }
        //     },
        //     {
        //         $lookup: { from: 'matches', localField: '_id', foreignField: '_id', as: 'users_info'}
        //     },
        //     {
        //         $unwind: "$users_info"
        //     },
        //     {
        //         $project: {
        //             NameMatch: "$users_info.name",
        //             Fecha: "$users_info.Date",
        //             pictureUrl: "$users_info.pictureUrl"
        //         }
        //     },
        //     {
        //         $sort: {"RESULT" : -1}
        //     }
        // ]).exec()

        // console.log(usersMvp);
        // return usersMvp;
//----------------------------------------------------------------------------------------------------
    // return await matchesModel.find(
    // {
    //       $and: [
    //          { _userMvp: {$ne: null }} ,
    //          { date: { $gte: fromDateNominado}},
    //          { date: { $lte: toDateNominado}},
    //         ]
    // })
    // .populate( {path:'_userMvp', select: 'firstName lastName pictureUrl'})
    // .limit(10000)
    // .exec()
//--------------------------------------------------------------------------------------------------------------------------------------------------

async function getTotalNominados(){
    try {
        // let totalNominatedUsers = await assessmentModel.count({ nominacionMVP: true }).exec();
        // return totalNominatedUsers;

        let usersMVP = await matchesModel.aggregate([
            {
              $group: {
                _id: "$_userMvp",
                totalNominated: { $sum: 1 }
              }
            },
            {
              $sort: { totalNominated: -1 }
            }
          ]);
          
          let usersMVPIds = usersMVP.map(value => mongoose.Types.ObjectId(value._id));
          let usersMVPFind = await usersModel.find({ _id: usersMVPIds, role: 'user' }).select('firstName lastName pictureUrl').exec();
          
          let usersMVPMap = usersMVPFind.map(value1 => {
            let filterUserMVP = usersMVP.find(value2 => JSON.stringify(value1._id) === JSON.stringify(value2._id));
            return {
              ...value1.toJSON(),
              numberNominated: filterUserMVP.totalNominated
            };
          });
          
          let MVPs = usersMVPMap.sort((a, b) => b.numberNominated - a.numberNominated);
          
          let result = {
            MVPs: MVPs,
            totalNominated: MVPs.length
          };
          
          return result;

    } catch (error) {
        return Promise.reject(error)
    }
}

















































