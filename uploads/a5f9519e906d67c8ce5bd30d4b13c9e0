import {Users, Exercises} from "../models";

// console.log(Users)
exports.createUser = (req,res) => { 
    const name = req.body.username;
    if(name !== ""){
        Users.find({username:name})
        .then(d => {
            if(d.length != 0){
                res.json("User already exists")
            }else{
                Users.create(req.body)
                .then(d => res.json(d))
                .catch(e =>  res.json(e));
            }
        })
        .catch(e => res.send(e))
    }else{
        res.json("User name cannot be blank")
    }
}
// Getting all users
exports.findUsers = (req,res) => {
    Users.find({})
    .then(datas => {
        res.json(datas)
    })
    .catch(err => res.send(err));
}

// Adding exercises to a specific user
exports.addExercises = async (req,res) => {
    const { userId, description, duration, date } = req.body;
    console.log(date,description,userId,duration);
    if(description === ""){
        res.json("You need to fill the description");
    }else if(duration === ""){
        res.json("please fill the duration");
    }else if(Number.isInteger(parseInt(duration)) === false){
         res.json("the duration must be a number");
    }else{
        try {
            const arrQuery = await Users.find({_id:userId});
            if(arrQuery.length != 0){
                const name = arrQuery[0].username;
                const id = arrQuery[0]._id;
                if(date === ""){
                   let dateString = new Date().toUTCString();
                    dateString = dateString.split(" ").slice(0, 4).join(" ");
                    try {
                         await Exercises.create({
                            username:name,
                            userId:id,
                            description:description,
                            duration:duration,
                            registerDate:dateString
                        })
                         res.json({
                            username: name,
                            description: description,
                            duration: duration,
                            _id: id,
                            registerDate: dateString
                          });
                    } catch (error) {
                        res.send(error.message);
                    }
                }else{
                    let datestring = new Date(date).toUTCString();
                    datestring = datestring.split(" ").slice(0,4).join(" ");
                    if(datestring == "Invalid Date"){
                         res.json({err:datestring});
                    }else{
                        await Exercises.create({
                            username: name,
                            description: description,
                            duration: duration,
                            userId: id,
                            registerDate: datestring,
                            date: datestring
                          });
                
                          res.json({
                            username: name,
                            description: description,
                            duration: duration,
                            _id: id,
                            registerDate: datestring
                          });
                    }
                }
            }else{
                res.json("User does not exist")
            }
        } catch (error) {
             res.json(error);
        }
        
    }
}
    

// Getting the logs of an particular userSelect
exports.logsOfUser = async (req,res) => {
    const id = req.query.userId;
    if(id){
        try {
            const data = await Exercises.find({userId:id})
            let uId = data[0].userId;
            let username = data[0].username;
            let logs = data.map( e => {
                return {
                description:e.description,
                duration:e.duration,
                registerDate:e.registerDate
                }
            })
    
             res.json({
                 username:username,
                 userId:uId,
                 logs:logs,
                 count:logs.length
             });
    
        } catch (error) {
            res.send(error)
        }
    }else{
         res.json({err:"Unkown User"});
    }
    
}

// Getting the values by parsing the queries
exports.userDetails = async (req,res) => {
    const { userId } = req.query;
    const { from } = req.query;
    const { to } =  req.query;
    const { limit } =  req.query;
    // find the excercise of a user with given userid
    const arrQuery = await Exercises.find({userId:userId});
    let finalResult;
    if(arrQuery.length != 0){
        // find all the data if the user exits
       const testArr = await Exercises.find({userId:userId})
       .select("_id description duration registerDate date");
        // Filter the data within the range of dates
        if(from == undefined || to == undefined){
            finalResult = testArr;
        }else{
            const objfrom = new Date(from);
            const objto = new Date(to);
            if(objfrom == "Invalid Date" || objto == "Invalid Date"){
                 res.json("Invalid date format");
            }else{
                finalResult = testArr.filter(filterByDate);
                // function to filter the dates
                function filterByDate(obj){
                    return objfrom < obj.date && obj.date  < objto? true : false;
                 }

            }

        }
        // Filter the data by limit  
        if(limit != undefined){
            finalResult = testArr.slice(0,limit)
        }

        const result = {
            _id:arrQuery[0].userId,
            username:arrQuery[0].username,
            count:finalResult.length,
            logs:finalResult
        } 
         res.json(result);
    
    }else{
         res.json("user does not exists");
    }
}
module.exports = exports;