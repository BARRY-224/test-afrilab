Suite à quelques erreurs de Configuration en local j'ai implémenté toutes l'API sur ce fichier.

## Configuration du projet ADONIS

npm init adonis-ts-app@latest recipe-api
cd recipe-api
npm install

node ace serve --watch

## Création des migrations 'recipes' et 'ingredients'
node ace make:migration recipes
node ace make:migration ingredients
## CODE 'recipes'
        import BaseSchema from '@ioc:Adonis/Lucid/Schema'

        export default class Recipes extends BaseSchema {
        protected tableName = 'recipes'

        public async up() {
            this.schema.createTable(this.tableName, (table) => {
            table.increments('id')
            table.string('title').notNullable()
            table.text('short').nullable()
            table.timestamp('created_at', { useTz: true }).defaultTo(this.now())
            table.timestamp('updated_at', { useTz: true }).defaultTo(this.now())
            })
        }

        public async down() {
            this.schema.dropTable(this.tableName)
        }
        }

## CODE 'ingredients'

        import BaseSchema from '@ioc:Adonis/Lucid/Schema'

        export default class Ingredients extends BaseSchema {
        protected tableName = 'ingredients'

        public async up() {
            this.schema.createTable(this.tableName, (table) => {
            table.increments('id')
            table.string('title').notNullable()
            table.string('unit').notNullable() // e.g., grams, liters
            table.timestamp('created_at', { useTz: true }).defaultTo(this.now())
            table.timestamp('updated_at', { useTz: true }).defaultTo(this.now())
            })
        }

        public async down() {
            this.schema.dropTable(this.tableName)
        }
        }

## Création des models

node ace make:model Recipe -m
node ace make:model Ingredient -m

## Model recipes

    import { BaseModel, column } from '@ioc:Adonis/Lucid/Orm'

    export default class Recipe extends BaseModel {
    @column({ isPrimary: true })
    public id: number

    @column()
    public title: string

    @column()
    public short: string
    }

## Model ingredients
    import { BaseModel, column } from '@ioc:Adonis/Lucid/Orm'

    export default class Ingredient extends BaseModel {
    @column({ isPrimary: true })
    public id: number

    @column()
    public title: string

    @column()
    public unit: string
    }

## Création des controllers 'recipes' and 'ingredients'
node ace make:controller Recipe
node ace make:controller Ingredient
## Controller 'RecipeController'

    import Recipe from 'App/Models/Recipe'

    export default class RecipeController {
    public async index() {
        return Recipe.all()
    }

    public async store({ request }) {
        const data = request.only(['title', 'short'])
        return Recipe.create(data)
    }

    public async show({ params }) {
        return Recipe.findOrFail(params.id)
    }

    public async update({ params, request }) {
        const recipe = await Recipe.findOrFail(params.id)
        const data = request.only(['title', 'short'])
        recipe.merge(data)
        await recipe.save()
        return recipe
    }

    public async destroy({ params }) {
        const recipe = await Recipe.findOrFail(params.id)
        await recipe.delete()
        return { message: 'Recipe deleted successfully' }
    }
    }
## Controller IngredientController
    import Ingredient from 'App/Models/Ingredient'

    export default class IngredientController {
    public async index() {
        return Ingredient.all()
    }

    public async store({ request }) {
        const data = request.only(['title', 'unit'])
        return Ingredient.create(data)
    }

    public async show({ params }) {
        return Ingredient.findOrFail(params.id)
    }

    public async update({ params, request }) {
        const ingredient = await Ingredient.findOrFail(params.id)
        const data = request.only(['title', 'unit'])
        ingredient.merge(data)
        await ingredient.save()
        return ingredient
    }

    public async destroy({ params }) {
        const ingredient = await Ingredient.findOrFail(params.id)
        await ingredient.delete()
        return { message: 'Ingredient deleted successfully' }
    }
    }

## Configuration des routes 'recipes' and 'ingredients'

    import Route from '@ioc:Adonis/Core/Route'

    // Routes for Recipes
    Route.get('/recipes', 'RecipeController.index')
    Route.post('/recipes', 'RecipeController.store')
    Route.get('/recipes/:id', 'RecipeController.show')
    Route.put('/recipes/:id', 'RecipeController.update')
    Route.delete('/recipes/:id', 'RecipeController.destroy')

    // Routes for Ingredients
    Route.get('/ingredients', 'IngredientController.index')
    Route.post('/ingredients', 'IngredientController.store')
    Route.get('/ingredients/:id', 'IngredientController.show')
    Route.put('/ingredients/:id', 'IngredientController.update')
    Route.delete('/ingredients/:id', 'IngredientController.destroy')

## ENDPOINTS
    - http://localhost:3333/recipes  pour les recettes
    - http://localhost:3333/ingredients pour les ingrédients



