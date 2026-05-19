# sdewdge#include <iostream>
#include <vector>
#include <string>
#include <cstdlib>
#include <ctime>
using namespace std;

struct Player {
    string name;
    int hp, ad, ap, armor, mr;
};

struct Card {
    string name;
    string type;
    void (*effect)(Player&, Player&);
};

void attack(Player& self, Player& target) {
    int dmg = self.ad - target.armor / 2;
    if (dmg < 0) dmg = 0;
    target.hp -= dmg;
    cout << self.name << "이(가) " << target.name << "에게 " << dmg << " 물리 피해!\n";
}

void magic(Player& self, Player& target) {
    int dmg = self.ap - target.mr / 2;
    if (dmg < 0) dmg = 0;
    target.hp -= dmg;
    cout << self.name << "이(가) " << target.name << "에게 " << dmg << " 마법 피해!\n";
}

void item_infinity_edge(Player& self, Player& target) {
    self.ad += 20;
    cout << self.name << "이(가) 무한의 검을 장착! AD +20\n";
}

void item_rabadon(Player& self, Player& target) {
    self.ap += 40;
    cout << self.name << "이(가) 라바돈의 죽음모자를 장착! AP +40\n";
}

void item_warmog(Player& self, Player& target) {
    self.hp += 200;
    cout << self.name << "이(가) 워모그의 갑옷을 장착! HP +200\n";
}

int main() {
    srand(time(0));
    vector<Player> players = {
        {"가렌", 100, 25, 5, 30, 20},
        {"애니", 80, 5, 30, 15, 25},
        {"드레이븐", 90, 30, 10, 20, 15},
        {"말파이트", 120, 15, 10, 40, 30}
    };

    vector<Card> deck = {
        {"공격", "스킬", attack},
        {"마법", "스킬", magic},
        {"무한의 검", "아이템", item_infinity_edge},
        {"라바돈의 죽음모자", "아이템", item_rabadon},
        {"워모그의 갑옷", "아이템", item_warmog}
    };

    cout << "=== 롤 갓필드 스타일 카드게임 ===\n";
    int turn = 0;
    while (true) {
        Player& current = players[turn % players.size()];
        Player& target = players[(turn + 1) % players.size()];
        if (current.hp <= 0) {
            cout << current.name << "은(는) 탈락했습니다!\n";
            break;
        }
        cout << "\n[" << current.name << "의 턴]\n";
        int cardIndex = rand() % deck.size();
        deck[cardIndex].effect(current, target);
        cout << target.name << " HP: " << target.hp << "\n";
        if (target.hp <= 0) {
            cout << target.name << "이(가) 쓰러졌습니다!\n";
            break;
        }
        turn++;
    }
    cout << "\n게임 종료!\n";
    return 0;
}
