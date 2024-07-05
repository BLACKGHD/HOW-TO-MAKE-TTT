first change this SWEP.Base = to ttt base 
SWEP.Base = "weapon_tttbase"

after that choose the weapons kind by SWEP.Kind = and after 
WEAPON_PISTOL: small arms like the pistol and the deagle.
WEAPON_HEAVY: rifles, shotguns, machineguns.
WEAPON_NADE: grenades.
WEAPON_EQUIP1: special equipment, typically bought with credits and Traitor/Detective-only.
WEAPON_EQUIP2: same as above, secondary equipment slot. Players can carry one of each.
WEAPON_ROLE: special equipment that is default equipment for a role, like the DNA Scanner.
WEAPON_MELEE: only for the crowbar players get by default.
WEAPON_CARRY: only for the Magneto-stick, default equipment.

after that pick ammo kind by copy this code SWEP.AmmoEnt =
choose the right ammo type from this list- 
item_ammo_pistol_ttt: Pistol and M16 ammo.
item_ammo_smg1_ttt: SMG ammo, used by MAC10 and UMP.
item_ammo_revolver_ttt: Desert eagle ammo.
item_ammo_357_ttt: Sniper rifle ammo.
item_box_buckshot_ttt: Shotgun ammo.

if you want to add custom icon to the swep 
follow this SWEP.Icon = "VGUI/ttt/icon_myserver_ak47"
its not the same to all addones all the addones have custom links you need to find it in the gmod directory.

for the icon to be downloaded if someone conected to the server you must add this - 
if SERVER then
      resource.AddFile("materials/VGUI/ttt/icon_myserver_ak47.vmt")
    end

if you want the weapon to be spawnable around the map you need to add must - 
 SWEP.AutoSpawnable = true 
and if not false 


the code = heavy and main arms 
-- TTT SWEP INFO 
SWEP.Base		= "weapon_tttbase"
SWEP.Kind               =  WEAPON_HEAVY
SWEP.AmmoEnt            = "item_ammo_pistol_ttt"
SWEP.AutoSpawnable      =  true 
SWEP.icon               =  "vgui/entities/ree_bullpup"
if SERVER then
	resource.AddFile("materials/VGUI/entities/ree_bullpup.vmt")
 end


the code for pistols and small arms 
-- TTT SWEP INFO 
SWEP.Base		= "weapon_tttbase"
SWEP.Kind               =  WEAPON_PISTOL
SWEP.AmmoEnt            = "item_ammo_pistol_ttt"
SWEP.AutoSpawnable      =  true 
SWEP.icon               =  "vgui/entities/ree_bullpup"
if SERVER then
	resource.AddFile("materials/VGUI/entities/ree_bullpup.vmt")
 end



for snipers or scoped weapons add this code 

function SWEP:SetZoom(state)
    if CLIENT then 
       return
    elseif IsValid(self.Owner) and self.Owner:IsPlayer() then
       if state then
          self.Owner:SetFOV(20, 0.3)
       else
          self.Owner:SetFOV(0, 0.2)
       end
    end
end

-- Add some zoom to ironsights for this gun
function SWEP:SecondaryAttack()
    if not self.IronSightsPos then return end
    if self.Weapon:GetNextSecondaryFire() > CurTime() then return end
    
    bIronsights = not self:GetIronsights()
    
    self:SetIronsights( bIronsights )
    
    if SERVER then
        self:SetZoom(bIronsights)
     else
        //self:EmitSound(self.Secondary.Sound)
    end
    
    self.Weapon:SetNextSecondaryFire( CurTime() + 0.3)
end

function SWEP:PreDrop()
    self:SetZoom(false)
    self:SetIronsights(false)
    return self.BaseClass.PreDrop(self)
end

function SWEP:Reload()
    self.Weapon:DefaultReload( ACT_VM_RELOAD );
    self:SetIronsights( false )
    self:SetZoom(false)
end


function SWEP:Holster()
    self:SetIronsights(false)
    self:SetZoom(false)
    return true
end

if CLIENT then
   local scope = surface.GetTextureID("sprites/scope")
   function SWEP:DrawHUD()
      if self:GetIronsights() then
         surface.SetDrawColor( 0, 0, 0, 255 )
         
         local x = ScrW() / 2.0
         local y = ScrH() / 2.0
         local scope_size = ScrH()

         -- crosshair
         local gap = 80
         local length = scope_size
         surface.DrawLine( x - length, y, x - gap, y )
         surface.DrawLine( x + length, y, x + gap, y )
         surface.DrawLine( x, y - length, x, y - gap )
         surface.DrawLine( x, y + length, x, y + gap )

         gap = 0
         length = 50
         surface.DrawLine( x - length, y, x - gap, y )
         surface.DrawLine( x + length, y, x + gap, y )
         surface.DrawLine( x, y - length, x, y - gap )
         surface.DrawLine( x, y + length, x, y + gap )


         -- cover edges
         local sh = scope_size / 2
         local w = (x - sh) + 2
         surface.DrawRect(0, 0, w, scope_size)
         surface.DrawRect(x + sh - 2, 0, w, scope_size)

         surface.SetDrawColor(255, 0, 0, 255)
         surface.DrawLine(x, y, x + 1, y + 1)

         -- scope
         surface.SetTexture(scope)
         surface.SetDrawColor(255, 255, 255, 255)

         surface.DrawTexturedRectRotated(x, y, scope_size, scope_size, 0)

      else
         return self.BaseClass.DrawHUD(self)
      end
   end

   function SWEP:AdjustMouseSensitivity()
      return (self:GetIronsights() and 0.2) or nil
   end
end

