from starkware.starknet.business_logic.contract_definition import StarknetContract
from starkware.starknet.definitions import starknet

# Definicja kontraktu
@starknet.contract
def MyContract():
    # Zmienna przechowująca liczbę
    count: public(uint256)

    # Funkcja inicjalizacyjna kontraktu
    def init():
        self.count = 0

    # Funkcja zwiększająca liczbę o określoną wartość
    @public
    @payable
    def increase_count(value: uint256):
        self.count += value

    # Funkcja zwracająca obecną wartość liczby
    @public
    @view
    def get_count() -> uint256:
        return self.count
