# A1-P1
// Part 1: validateProperty function
function validateProperty(obj, propValidator) {
    if (obj.hasOwnProperty(propValidator.name)) {
        return propValidator.validators.every(validator => validator(obj[propValidator.name]));
    }
    return false;
}

// Test validateProperty function
const validator = {name : "p1" , validators: [s => typeof s == 'string' && s.length > 2, s => s[0]=="a"]};
const obj1 = { p1 : "abc" };
const obj2 = { p2 : 123 };
const obj3 = { p1 : "a" , p2 : 123 };

console.log(validateProperty(obj1, validator)); // true
console.log(validateProperty(obj2, validator)); // false
console.log(validateProperty(obj3, validator)); // false

// Part 2: copyProperties function
function copyProperties(obj, propValidators) {
    return propValidators.reduce((newObj, propValidator) => {
        if (validateProperty(obj, propValidator)) {
            newObj[propValidator.name] = obj[propValidator.name];
        }
        return newObj;
    }, {});
}

// Test copyProperties function
const validators = [
   { name: "p1", validators: [s => typeof s == 'string' && s.length > 2, s => s[0] == "a"] },
   { name: "p2", validators: [s => Number.isInteger(s)] }
];

const obj4 = { p1: "a" };
const obj5 = { p1: 123 };
const obj6 = { p1: "abc", p2: 123 };

console.log(copyProperties(obj4, validators)); // {}
console.log(copyProperties(obj5, validators)); // {}
console.log(copyProperties(obj6, validators)); // { p1: 'abc', p2: 123 }

// Part 3: Add copyProperties method to Object type
Object.prototype.copyProperties = function(propValidators) {
    return copyProperties(this, propValidators);
};

// Test Object.copyProperties method
console.log(obj4.copyProperties(validators)); // {}
console.log(obj5.copyProperties(validators)); // {}
console.log(obj6.copyProperties(validators)); // { p1: 'abc', p2: 123 }
// Add associateWith method to Array type
Array.prototype.associateWith = function(transformation) {
    return this.reduce((obj, item) => {
        obj[item] = transformation(item);
        return obj;
    }, {});
};

// Test associateWith method
let numbers = ["one", "two", "three", "four"];
console.log(numbers.associateWith(str => str.length)); // { one: 3, two: 3, three: 5, four: 4 }

// Implement checkUsersValid function
function checkUsersValid(goodUsers) {
    return function allUsersValid(submittedUsers) {
        let goodUserIds = goodUsers.map(user => user.id);
        return submittedUsers.every(submittedUser => goodUserIds.includes(submittedUser.id));
    };
}

// Test checkUsersValid function
var goodUsers = [
   { id: 1 },
   { id: 2 },
   { id: 3 }
];

var testAllValid = checkUsersValid(goodUsers);

console.log(testAllValid([
   { id: 2 },
   { id: 1 }
])); // true

console.log(testAllValid([
   { id: 2 },
   { id: 4 },
   { id: 1 }
])); // false

// Implement Spy function
function Spy(target, method) {
    let originalFunction = target[method];
    let result = {
        count: 0
    };

    target[method] = function() {
        result.count++;
        return originalFunction.apply(this, arguments);
    };

    return result;
}

// Test Spy function
var spy = Spy(console, 'error');

console.error('calling console.error');
console.error('calling console.error');
console.error('calling console.error');

console.log(spy.count); // 3
