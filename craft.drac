<drac2>
if ctx.author.id not in [486296335180627972, 306209506965061633] and not get_svar("crafting"):
    err("Haha. April Fools!")
a, n, out = &ARGS&, "\n", []
cvar = "crafting_data"
cooldown = 24
t = time()
stats = load_json(get(cvar, {}))
bonus = character().levels.get("Artificer") >= 10

data = [
        {
         "rarity": "common",
         "weeks": 1,
         "cost": 50
        },
        {
         "rarity": "uncommon",
         "weeks": 2,
         "cost": 200
        },
        {
         "rarity": "rare",
         "weeks": 10,
         "cost": 2000
        },
        {
         "rarity": "very rare",
         "weeks": 25,
         "cost": 20000
        }
       ]

if character().skills.arcana.prof < 1:
    out.append(f''' -title "You lack the required skills!"
                    -desc "To craft an item, you need to be proficient in arcana checks."
                    -footer "{ctx.prefix}help {ctx.alias}" ''')
    
elif t < stats.get("cooldown", 0):
    timer = ceil(stats.get("cooldown", 0) - t) // 60
    timer = f'{timer} minutes' if timer < 60 else timer // 60
    timer = timer if not str(timer).isdigit() else f'{timer} hours'
    
    out.append(f''' -title "Take a Breather!"
                    -desc "An insufficient amount of time has passed since the last time you worked on your crafting."
                    -f "Cooldown|{timer}"
                    -footer "{ctx.prefix}help {ctx.alias}" ''')

elif stats: # New crafting day. If day % 5, roll 1d20
    item = stats["item"]
    remaining = stats["remaining"] - 1
    cooldown = t + (cooldown * 3600)
    fail = False
    
    for x in data:
        
        if x["rarity"] == item:
            weeks = x["weeks"]
            days = weeks * 7
            break

    if not (days - remaining) % 5: # complication
        r = vroll("1d20")
        
        if remaining:
            out.append(f''' -desc "**Roll:** {r}" ''')
        
        if r.total == 1:
            out.append(f''' -f "Outcome|You encountered a complication while you were crafting. You failed to make any meaningful progress today. Please try again tomorrow." ''')
            fail = True
        
        elif remaining:
            stats.update({"remaining": remaining})
            out.append(f''' -f "Outcome|You finished another day of crafting without any complications. Please return tomorrow to continue." ''')
            
        else:
            out.append(f''' -desc "Congratulations! You have finally finished crafting your {item} item. You will need to manually add the item to your inventory." ''')
            character().delete_cvar(cvar)
            
    else:
        
        if remaining:
            stats.update({"remaining": remaining})
            out.append(f''' -desc "You finished another day of crafting without any complications. Please return tomorrow to continue." ''')
        
        else:
            out.append(f''' -desc "Congratulations! You have finally finished crafting your {item} item. You will need to manually add the item to your inventory." ''')
            character().delete_cvar(cvar)
    
    if remaining:
        stats.update({"cooldown": cooldown})
        character().set_cvar(cvar, dump_json(stats))
        out.append(f''' -f "Days Remaining|{remaining}" ''')
    
    out.append(f''' -title "{name} continues crafting their {item} item!" -footer "{ctx.prefix}{ctx.alias}" ''')
        

elif a: # Crafting new thingy
    rarity = ""
    scroll = False
    
    for x in a:
    
        if x.lower() == "scroll":
            scroll = True
            a.remove(x)
            break
    
    for x in ["common", "uncommon", "rare", "very rare"]:
    
        if x.startswith(a[0].lower()):
            rarity = x
            break
            
    if not rarity:
        return f'''help {ctx.alias} -here'''
        
    else:
        stats = {}
        
        for x in data:
        
            if x.rarity == rarity:
                stats = x
        
        days = (stats.get("weeks", 0) * 7) - 1
        cost = stats.get("cost", 0)
        coinvar = load_json(get("bags",[]))
        coinbag = []
        
        if scroll:
            days = ceil(days / 2)
            cost = ceil(cost / 2)
            
        if bonus and rarity in ["common", "uncommon"]:
            days = ceil(days / 4)
            cost = ceil(cost / 2)
            
        
        for x in coinvar:
        
            if x[0] == "Coin Pouch":
                coinbag = x
                break
        
        if coinbag:
            coins = coinbag[1].get("gp", 0)
            
            if coins >= cost:
                x[1].update({"gp": coins - cost})
                saved = {"item": rarity, "day": 0, "remaining": days, "cooldown": t + cooldown * 3600}
                character().set_cvar(cvar, dump_json(saved))
                out.append(f''' -title "{name} has started crafting {'an' if rarity[0] in ['a', 'e', 'i', 'o', 'u'] else 'a'} {rarity} item!"
                                -desc "Your first day of crafting has been completed. Please return tomorrow to continue."
                                -f "Days Remaining|{days}|inline"
                                -f "Cost|{cost} gp|inline"
                                -f "{coinbag[0]} (-{cost} gp)|{n.join([f'{coinbag[1][x]} {x}' for x in coinbag[1]])}"
                                -footer "{ctx.prefix}{ctx.alias} {rarity} | {cost} gp has been removed from your {coinbag[0]}" ''')
                
            else:
                coinvar.append(["Coin Pouch", {"cp": 0, "sp": 0, "ep": 0, "gp": 0, "pp": 0}])
                out.append(f''' -title "{name} tries to start crafting!"
                                -desc "You do not have enough money to begin crafting {'an' if rarity[0] in ['a', 'e', 'i', 'o', 'u'] else 'a'} {rarity} item."
                                -f "Cost|{cost} gp"
                                -footer "{ctx.prefix}help {ctx.alias}" ''')
                
            character().set_cvar("bags", dump_json(coinvar))

else:
    return f'''help {ctx.alias} -here'''

return f'''embed {n.join(out)} -thumb {image} '''
</drac2>
